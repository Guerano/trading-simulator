# 0007. Storage

- Status: Accepted
- Date: 2026-09-28

## Context

The simulator needs to persist two different kinds of data with different access
patterns: historical market data (klines/candlesticks and trades, used in bulk for
deterministic replay) and account state (positions, balances, orders, used for
transactional reads and writes during live paper trading).

## Decision

For historical market data: source klines and trades from the public
`data.binance.vision` archive, supplemented by the simulator's own live recordings
(trades and L2 order book snapshots/updates). Store this data in Apache Parquet, a
columnar file format well suited to bulk analytical reads, starting from v0.3 (not
required for the MVP).

For account state: store positions, balances, and orders in SQLite starting from
v0.2. For the MVP itself, account state lives only in memory and is not persisted
across restarts.

## Alternatives considered

- Store market data in SQLite alongside account state — rejected: SQLite is row-
  oriented and not well suited to the bulk columnar scans that replay and analysis
  workloads need; Parquet is purpose-built for this.
- Persist account state to Parquet — rejected: Parquet is not designed for
  transactional point reads/writes; SQLite is a better fit for account state's
  access pattern.
- Persist account state from the MVP — rejected: adds scope to the MVP before the
  account model has stabilized; in-memory state is sufficient to validate the
  engine first.

## Consequences

Positive: each storage engine is matched to its workload (columnar bulk data vs.
transactional state); using the public Binance archive avoids re-downloading
historical data the simulator did not itself record.

Negative: two different storage technologies must be maintained; the MVP's
in-memory-only account state means no session persists across a restart, which
constrains how the CLI can be used before v0.2 lands.

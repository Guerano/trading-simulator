# 0004. Unified engine for live and replay

- Status: Accepted
- Date: 2026-09-28

## Context

The simulator must support two modes: paper trading against live market data, and
deterministic replay of historical data for backtesting strategies. Maintaining two
separate execution engines would double the maintenance burden and risk behavioral
divergence between what a strategy experiences live and what it experienced in a
backtest.

## Decision

Use the same `Strategy` trait and the same simulated execution engine for both live
paper trading and historical replay. The market data source and the clock are
abstracted behind traits, so the engine itself is agnostic to whether ticks arrive
from a live WebSocket feed or from a historical data file; replay is deterministic
because both the data feed and the clock are driven by the replayed sequence rather
than wall-clock time.

For the minimum viable product (MVP), no automated strategies run yet: orders are
entered manually through the command-line interface (CLI), but they pass through the
same order interface that strategies will use once implemented.

## Alternatives considered

- Separate live and backtest engines — rejected: duplicates fill logic and risks the
  two diverging silently, undermining confidence that backtest results predict live
  behavior.
- Record-and-replay at the network layer only (replaying raw Binance messages
  through the live code path unmodified) — rejected: couples replay determinism to
  network-layer details and timing, and does not by itself abstract the clock, so
  time-based logic could still leak wall-clock dependencies.

## Consequences

Positive: a strategy written once behaves identically live and in replay; fill logic
and risk checks are tested once; deterministic replay makes backtests reproducible
and debuggable.

Negative: the abstraction over data source and clock adds indirection that live-only
code would not need, and any behavior that is legitimately different between live
and replay (e.g. handling of feed gaps) must be modeled explicitly rather than
special-cased.

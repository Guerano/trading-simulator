# trading-simulator

[![CI](https://github.com/OWNER/trading-simulator/actions/workflows/ci.yml/badge.svg)](https://github.com/OWNER/trading-simulator/actions/workflows/ci.yml)

A trading simulator written in Rust. It executes fictitious orders against **live market data**
(paper trading) and, later, replays **historical data** deterministically so that automated
strategies can be developed and backtested with the same execution engine.

Crypto markets first (Binance); the design keeps room for other venues and asset classes.

> **Status:** early development — no usable release yet.

## Features (planned for v0.1.0)

- Live Binance trades and Level 2 (L2) order book feed.
- Interactive read-eval-print loop (REPL) command line: market and limit orders, cancel, open
  orders, balances.
- Realistic simulated fills: market orders walk the real order book, limit orders fill only when a
  public trade crosses through the limit price, configurable maker/taker fees.

## Roadmap

| Version | Scope |
|---------|-------|
| v0.0.x  | Workspace scaffold, CI, fixed-point `Price` / `Qty` types |
| v0.1.0  | MVP: live Binance trades + L2, REPL (market / limit / cancel / orders / balance), in-memory account |
| v0.2    | Persistent account (SQLite), profit and loss (PnL) |
| v0.3    | Live data recording, historical data import (Apache Parquet) |
| v0.4    | Historical replay, simulated clock, `Strategy` trait, backtest report |
| v0.5    | Stop orders |
| v0.6    | Second venue |
| v0.7    | Daemon with an API |
| v0.8+   | Graphical user interface |

## Building

The Rust toolchain version is pinned in [`rust-toolchain.toml`](rust-toolchain.toml); `rustup`
installs it automatically.

```sh
cargo build --release
cargo test --workspace
```

Pre-built binaries for Windows and Linux are attached to each
[GitHub release](https://github.com/OWNER/trading-simulator/releases).

## Documentation

- [`ARCHITECTURE.md`](ARCHITECTURE.md) — crate layout and data flow.
- [`docs/adr/`](docs/adr/) — Architecture Decision Records (ADR): every significant choice and its rationale.
- [`docs/design/`](docs/design/) — per-feature design documents (diagrams and specification).

## Contributing

GitHub Flow: one feature per branch and pull request, green CI required, squash merge.
Commit messages and pull request titles follow [Conventional Commits](https://www.conventionalcommits.org/);
versions follow [Semantic Versioning](https://semver.org/).

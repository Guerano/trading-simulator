# 0002. Workspace and toolchain

- Status: Accepted
- Date: 2026-09-28

## Context

The project needs a code layout that separates the simulation engine (pure domain
logic) from I/O-heavy adapters and from the user-facing binary, so the engine can be
tested and reused without pulling in networking or terminal concerns. It also needs
a reproducible Rust toolchain across machines and CI.

## Decision

Use a Cargo workspace with three crates, each prefixed `tsim-`:

- `tsim-core`: the simulation engine and domain types (orders, fills, instruments,
  the `Strategy` trait). No I/O.
- `tsim-binance`: the Binance market data and (later) execution adapter.
- `tsim-cli`: the binary crate, producing the `tsim` executable (REPL, wiring,
  configuration).

Target Rust edition 2024. Pin the toolchain with a `rust-toolchain.toml` at the
workspace root so `rustup` selects the same compiler version everywhere. All crates
set `publish = false`; none are published to crates.io.

## Alternatives considered

- Single crate — rejected: mixes engine, I/O, and CLI concerns, making the engine
  harder to unit-test in isolation and harder to reuse from a future GUI or daemon.
- Crate names without a common prefix — rejected: a `tsim-` prefix keeps crates
  grouped and unambiguous in dependency listings and search results.
- Floating toolchain (whatever `rustup default` resolves to) — rejected: risks
  behavior drift between contributor machines and CI as new Rust versions ship.

## Consequences

Positive: clear compile-time boundary between engine and I/O; `tsim-core` can be
tested and benchmarked without network access; adapters and the binary can evolve
independently; reproducible builds via a pinned toolchain.

Negative: workspace boilerplate (multiple `Cargo.toml` files, path dependencies) and
slightly more ceremony than a single crate; the toolchain pin must be bumped
deliberately when adopting new language features.

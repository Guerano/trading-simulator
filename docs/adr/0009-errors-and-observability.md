# 0009. Errors and observability

- Status: Accepted
- Date: 2026-09-28

## Context

The workspace has library crates (`tsim-core`, `tsim-binance`) consumed by other
code, and a binary crate (`tsim-cli`) consumed only by the end user. These two
contexts call for different error-handling styles: libraries should expose typed,
matchable errors; the binary just needs to report failures clearly and exit. The
project also needs a consistent way to emit runtime diagnostics.

## Decision

Use `thiserror` to define structured, typed error enums in the library crates
(`tsim-core`, `tsim-binance`), so callers can match on specific failure variants.
Use `anyhow` in the binary crate (`tsim-cli`) to propagate and report errors without
defining new error types at that layer. Use `tracing` for structured logging
throughout, rather than ad hoc `println!`/`eprintln!` calls.

## Alternatives considered

- `anyhow` throughout, including library crates — rejected: erases error type
  information at the library boundary, making it harder for callers (including
  future GUI or daemon code) to handle specific failure cases programmatically.
- `thiserror` in the binary as well — rejected: the binary has no callers of its own
  API; defining typed errors there adds ceremony without benefit.
- Plain `println!`/`eprintln!` logging — rejected: no severity levels, no
  structured fields, and no way to redirect or filter output as the project grows.

## Consequences

Positive: library consumers get precise, matchable error types; the binary's error
handling stays lightweight; `tracing` gives structured, filterable logs that scale
from CLI use to a future daemon.

Negative: two different error-handling idioms exist in the same workspace, which
contributors must learn to apply in the right place; `tracing` has a steeper setup
than plain print statements.

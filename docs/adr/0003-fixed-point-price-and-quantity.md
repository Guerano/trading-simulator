# 0003. Fixed-point price and quantity

- Status: Accepted
- Date: 2026-09-28

## Context

Order prices and quantities must be represented exactly. Market data and account
balances are compared, summed, and matched against exact thresholds (e.g. a limit
price), so representation error is unacceptable. Each traded instrument also has its
own precision (Binance defines a tick size and step size per symbol).

## Decision

Represent prices and quantities as newtypes wrapping a 64-bit signed integer,
`Price(i64)` and `Qty(i64)`, storing the value in the instrument's smallest unit at a
per-instrument scale (number of decimal places) carried alongside the instrument
definition. All arithmetic on these types goes through checked operations
(`checked_add`, `checked_sub`, `checked_mul`) that return an error on overflow
rather than wrapping or panicking silently.

## Alternatives considered

- `f64` — rejected: binary floating point cannot represent most decimal fractions
  exactly, so repeated arithmetic (summing fills, computing fees) accumulates
  rounding error that is unacceptable for financial quantities and for exact price
  comparisons.
- `rust_decimal` (or similar arbitrary-precision decimal crate) — rejected: carries
  more runtime overhead and a larger in-memory representation than a plain `i64`,
  and hides the scale inside the value rather than making it an explicit,
  per-instrument property that the engine controls; less explicit about overflow
  behavior at the call site.

## Consequences

Positive: exact arithmetic with no rounding error, small and cache-friendly
representation, explicit and auditable overflow handling, per-instrument precision
kept out of the value type and next to the instrument metadata where it belongs.

Negative: every arithmetic operation must go through the checked API rather than
using native operators directly, and the scale must be tracked and applied correctly
wherever a `Price` or `Qty` is formatted or parsed; mixing values from instruments
with different scales requires explicit conversion.

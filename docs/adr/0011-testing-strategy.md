# 0011. Testing strategy

- Status: Accepted
- Date: 2026-09-28

## Context

The project needs a testing approach that covers unit-level correctness (especially
for fixed-point arithmetic and the fill model, where subtle bugs would silently
corrupt results), integration with real-world Binance message shapes, and, later,
performance regressions. It also needs a convention that keeps test code from
cluttering production modules.

## Decision

Place unit tests in separate `src/**/tests.rs` files alongside the modules they
test, rather than inline `#[cfg(test)] mod tests` blocks, to keep test code
physically apart from production code. Use `proptest` for property-based testing of
fixed-point arithmetic and the fill model, where invariants (e.g. no silent
overflow, conservative fills) matter more than fixed example cases. Add integration
tests that run against recorded Binance messages to validate the market data
adapter against real payloads. Add `criterion` benchmarks starting from v0.4.
Measure coverage with `cargo-llvm-cov` and report it, but do not block CI on a
coverage threshold.

From v0.2 onward, follow a test-first cycle for new engine logic: write the
interface first (function/trait signatures with `todo!()` bodies and doc comments
stating the contract), then write failing tests against that interface, then
implement until the tests pass.

## Alternatives considered

- Inline `#[cfg(test)] mod tests` blocks — rejected: mixes test and production code
  in the same file, which the project explicitly wants to avoid.
- Example-based tests only, no property testing — rejected: fixed-point arithmetic
  and fill logic have edge cases (overflow boundaries, price-crossing edge cases)
  that are easy to miss by hand and well suited to property-based generation.
- Blocking CI on a coverage threshold — rejected: coverage percentage is a weak
  proxy for test quality; reporting it without blocking avoids incentivizing
  low-value tests written just to raise a number.
- Benchmarks from the MVP — rejected: performance work is premature before the core
  design (v0.1–v0.3) has stabilized.

## Consequences

Positive: test code is easy to locate and does not clutter production modules;
property tests catch edge cases example tests would miss; integration tests against
real recorded data catch adapter regressions early; the test-first cycle keeps
interfaces deliberately designed before implementation.

Negative: separate test files require consistent module wiring (`mod tests;`
declarations) to stay compiled; property tests can be slower and harder to debug
than example tests when they fail; unblocked coverage reporting relies on
discipline rather than an enforced gate.

# 0006. Market data source abstraction

- Status: Accepted
- Date: 2026-09-28

## Context

The simulator targets cryptocurrency markets first, starting with Binance, but
should not hard-wire assumptions that would prevent adding other markets (including
traditional stock markets) later. Different instruments may eventually need
different data sources, and a future version may want to switch sources without
restarting.

## Decision

Define a `MarketDataSource` trait that the engine depends on instead of depending on
Binance directly. Implement Binance first, since crypto is the initial target. The
data source is chosen per instrument in configuration at startup. A registry
allowing hot-switching of a data source at runtime is deferred to a later version;
the trait is designed so that adding it later does not require reshaping the
abstraction.

## Alternatives considered

- Depend on the Binance client directly throughout the engine — rejected: would
  require an engine rewrite to support any other exchange or asset class, contrary
  to the stated goal of eventually supporting stock markets.
- Build the hot-switching registry now — rejected: adds complexity not needed for
  the MVP; startup-time source selection is sufficient until multiple live sources
  per running instance are actually needed.

## Consequences

Positive: the engine is decoupled from any single exchange; adding a new market data
source later means implementing one trait, not modifying the engine; the design
leaves room for runtime source switching without a breaking change.

Negative: the trait boundary must be designed conservatively up front to anticipate
non-crypto markets (e.g. trading sessions, different tick conventions) even though
only Binance is implemented initially, which risks over-abstracting before a second
implementation exists to validate the boundary.

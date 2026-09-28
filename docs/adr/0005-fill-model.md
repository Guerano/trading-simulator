# 0005. Fill model

- Status: Accepted
- Date: 2026-09-28

## Context

Simulated orders must be filled against real market data in a way that is
conservative enough to be trustworthy, without modeling full market microstructure.
The simulator assumes its own orders are fictitious and of small volume relative to
the market, so they do not move the market (no market impact). Queue position for
resting limit orders cannot be known from public data alone.

## Decision

Model orders as having no market impact. Market orders walk the real Level 2 (L2)
order book (consuming visible liquidity level by level) to compute their fill price.
Limit orders fill only when a public trade prints strictly through the limit price
(i.e. at a price better than the limit, not merely equal to it), which is a
conservative approximation given that true queue position at the limit price is
unknown. Maker and taker fees are configurable per instrument or account, defaulting
to Binance's published fee schedule.

For the MVP, only market and limit orders are supported. Stop orders are deferred
and will be implemented later as conditional orders layered on top of market/limit
orders rather than as a distinct order type in the fill engine.

## Alternatives considered

- Fill limit orders as soon as the market price touches the limit — rejected: assumes
  the simulated order is at the front of the queue, which overstates fill
  probability and produces optimistic backtest results.
- Model market impact for the simulator's own orders — rejected: adds substantial
  complexity for a simulator whose stated use case is small, fictitious order
  volume; not justified for the MVP.
- Implement stop orders as a first-class order type in the fill engine from the
  start — rejected: increases MVP scope; a conditional-order layer on top of
  market/limit is simpler and can be added once the base fill model is proven.

## Consequences

Positive: fill logic is simple, auditable against real market data, and biased
conservative rather than optimistic, which is the safer direction for a paper
trading tool.

Negative: limit fills may be more pessimistic than a well-queued real order would
achieve, understating some strategies' real-world performance; deferring stop orders
means strategies needing them cannot be evaluated until that layer exists.

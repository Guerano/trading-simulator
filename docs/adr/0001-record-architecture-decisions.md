# 0001. Record architecture decisions

- Status: Accepted
- Date: 2026-09-28

## Context

The trading-simulator project will accumulate significant technical decisions over
time: workspace layout, data representations, execution model, storage engine, and
release process. Without a durable record, the reasoning behind these choices is
lost, and later contributors (or the original author, months later) re-litigate
settled questions or violate constraints that were never written down.

## Decision

Record every significant architecture decision as an Architecture Decision Record
(ADR), following the lightweight style proposed by Michael Nygard: a short,
numbered, immutable document per decision, stored in `docs/adr/`. Each ADR states
the context, the decision, the alternatives considered and why they were rejected,
and the consequences (positive and negative). New decisions get a new ADR; existing
ones are never edited after acceptance. If a decision is reversed or replaced, a new
ADR supersedes the old one, and the old one is marked accordingly.

## Alternatives considered

- No written record, relying on commit messages and memory — rejected: commit
  messages capture the "what", rarely the "why", and are hard to browse by topic.
- A single running `DECISIONS.md` file — rejected: a single mutable file loses
  history of superseded decisions and grows unwieldy.
- Wiki pages outside the repository — rejected: decisions drift out of sync with the
  code they describe and are not reviewed as part of pull requests.

## Consequences

Positive: decisions and their rationale are versioned alongside the code, reviewed
in pull requests, and easy to reference. Onboarding is faster because the "why" is
explicit.

Negative: adds a small amount of process overhead per significant decision, and
requires discipline to keep ADRs immutable and to write superseding records rather
than silently editing history.

# 0012. Feature development cycle

- Status: Accepted
- Date: 2026-09-28

## Context

As the project grows beyond the MVP, features need a consistent development process
so that design intent is captured before code is written, reviewed alongside the
implementation, and eventually rolled up into project-level architecture
documentation.

## Decision

Develop each feature through the following cycle, on a dedicated feature branch:

1. Write a design document at `docs/design/<feature>.md`, containing Mermaid
   diagrams and a specification, and commit it first on the feature branch.
2. Define the interface (signatures and contracts) for the feature.
3. Write tests against that interface (see ADR 0011).
4. Implement the feature until tests pass.
5. Code review, which includes checking conformance to the design document's
   specification, not just code quality.
6. Open a pull request.

One feature corresponds to one PR. When a milestone completes, its constituent
feature design documents are rolled up into an overview that feeds into the
project's `ARCHITECTURE.md`.

## Alternatives considered

- Implementation-first, design document after the fact (or none at all) — rejected:
  loses the benefit of thinking through the design and its diagrams before writing
  code, and reviewers lose a specification to check the implementation against.
- Multiple features per PR — rejected: makes review harder and couples unrelated
  changes' merge timing together.
- Skip the milestone rollup into `ARCHITECTURE.md` — rejected: without it,
  project-level architecture understanding would only exist scattered across
  per-feature design documents, with no single up-to-date overview.

## Consequences

Positive: design intent is written down and reviewable before implementation
begins; code review can check spec conformance, not just code quality; project
architecture documentation stays current via milestone rollups.

Negative: adds a documentation step before implementation can start, which slows
down small or exploratory features; requires discipline to keep the design document
and the eventual implementation in sync when the design changes mid-implementation.

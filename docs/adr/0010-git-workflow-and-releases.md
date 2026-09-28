# 0010. Git workflow and releases

- Status: Accepted
- Date: 2026-09-28

## Context

The project is hosted as a public GitHub repository and needs a consistent
contribution workflow, commit convention, versioning scheme, and release process
that can be automated by continuous integration (CI).

## Decision

Use GitHub Flow: short-lived feature branches merged into the main branch via pull
request (PR), requiring a green CI run before merge. Before merging, rebase the
branch onto the main branch, then merge with a merge commit (`--no-ff`): the
feature's commits stay individually visible in a linear sequence, and the merge
commit marks the PR boundary. Every commit, not only the PR title, follows the
Conventional Commits specification. Version releases using Semantic Versioning, with
v0.1.0 designating the MVP. Build release binaries for Windows and Linux only, built
by CI on any tag matching `v*`; macOS is not supported.

## Alternatives considered

- Git Flow with long-lived `develop`/`release` branches — rejected: unnecessary
  overhead for a project without parallel release trains.
- Squash merge — rejected: collapses a PR into a single commit and discards the
  step-by-step history of the feature (design, interface, tests, implementation),
  which is valuable for review and `git bisect`.
- Rebase merge without merge commit — rejected: linear, but the PR boundary
  disappears, making a whole feature harder to identify and revert.
- Plain merge commit without rebase — rejected: interleaved history is harder to
  read and bisect.
- Free-form commit messages — rejected: Conventional Commits enables automated
  changelog generation and makes intent (feature, fix, breaking change) explicit at
  a glance.
- Building macOS binaries — rejected: no current target audience or maintainer
  capacity to test on macOS; can be reconsidered later if demand appears.

## Consequences

Positive: predictable, low-ceremony workflow; CI-gated merges keep main always
green; Conventional Commits and Semantic Versioning enable future automation
(changelogs, release notes); release binaries are produced automatically on tag.

Negative: every commit must be clean and follow Conventional Commits (fixup
commits are squashed locally with interactive rebase before merge); branches
must be rebased before merging; a whole PR is reverted with `git revert -m 1`;
Windows/Linux-only releases mean macOS users must build from source.

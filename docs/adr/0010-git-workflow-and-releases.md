# 0010. Git workflow and releases

- Status: Accepted
- Date: 2026-09-28

## Context

The project is hosted as a public GitHub repository and needs a consistent
contribution workflow, commit convention, versioning scheme, and release process
that can be automated by continuous integration (CI).

## Decision

Use GitHub Flow: short-lived feature branches merged into the main branch via pull
request (PR), requiring a green CI run before merge. Merge PRs with squash merge, so
each PR becomes one commit on the main branch. Write commits following the
Conventional Commits specification. Version releases using Semantic Versioning, with
v0.1.0 designating the MVP. Build release binaries for Windows and Linux only, built
by CI on any tag matching `v*`; macOS is not supported.

## Alternatives considered

- Git Flow with long-lived `develop`/`release` branches — rejected: unnecessary
  overhead for a project without parallel release trains.
- Merge commits instead of squash merge — rejected: squash merge keeps main-branch
  history one commit per PR, simpler to read and to revert.
- Free-form commit messages — rejected: Conventional Commits enables automated
  changelog generation and makes intent (feature, fix, breaking change) explicit at
  a glance.
- Building macOS binaries — rejected: no current target audience or maintainer
  capacity to test on macOS; can be reconsidered later if demand appears.

## Consequences

Positive: predictable, low-ceremony workflow; CI-gated merges keep main always
green; Conventional Commits and Semantic Versioning enable future automation
(changelogs, release notes); release binaries are produced automatically on tag.

Negative: contributors must learn Conventional Commits formatting; squash merge
discards intermediate commit history from a PR; Windows/Linux-only releases mean
macOS users must build from source.

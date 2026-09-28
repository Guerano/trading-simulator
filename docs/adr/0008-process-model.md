# 0008. Process model

- Status: Accepted
- Date: 2026-09-28

## Context

The simulator needs a way for a user to interact with the engine (enter orders,
inspect state) while the engine itself processes market data and, later, drives
automated strategies. The eventual goal includes a graphical user interface (GUI),
which will need a stable way to talk to the engine, likely from a separate process.

## Decision

For the MVP, run as a single-process read-eval-print loop (REPL): the user interacts
with the `tsim` binary directly in a terminal. Internally, the engine lives in a
library crate (`tsim-core`) and is driven through `tokio` asynchronous channels —
commands flow in, events flow out — so the REPL is just one possible caller of that
channel interface, not something the engine depends on directly.

A separate daemon process exposing an application programming interface (API) is
planned for the GUI milestone (v0.7), at which point the channel-driven engine can
be wrapped by a network-facing service instead of a REPL, without changing the
engine's internals.

## Alternatives considered

- Build the daemon/API from the start — rejected: adds process-management,
  networking, and API-versioning concerns before there is a client (the GUI) that
  needs them; premature for the MVP.
- Call engine functions directly from the REPL without a channel interface —
  rejected: would couple the REPL tightly to the engine's internal API, making the
  later move to a daemon a larger rewrite instead of swapping the channel consumer.

## Consequences

Positive: the MVP stays simple to build and run (one process, no networking); the
channel-based command/event interface already matches the shape a daemon's API
would need, so the v0.7 migration mainly adds a transport rather than restructuring
the engine.

Negative: until v0.7, only one local client (the REPL) can drive the engine; no
remote or concurrent access is possible in the interim.

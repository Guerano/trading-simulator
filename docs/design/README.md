# Design documents

One document per feature, named `<feature>.md`, committed first on the feature branch before any
code. Each contains:

- **Diagrams** (Mermaid): class, sequence or state diagrams — whichever fits the feature.
- **Specification**: behaviour, invariants, edge cases and error handling, precise enough to
  derive tests from.
- **Out of scope**: what the feature deliberately does not cover.

Code review checks the implementation against its design document.

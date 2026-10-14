# Iteration-Level Scheduling

## 2026-08

- [[paper-vault/papers/yu2022orca]]
  - Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  - Role in paper: Core request-scheduling policy in Orca.
  - Mechanism: Rebuilds the runnable batch after each generated token, retiring completed requests and admitting waiting ones.
  - Why listed here: Removes request-level head-of-line idle capacity and establishes the scheduling basis for continuous batching.

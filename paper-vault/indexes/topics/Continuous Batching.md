# Continuous Batching

## 2026-08

- [[paper-vault/papers/yu2022orca]]
  - Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  - Topic focus: Scheduler-side admission at autoregressive iteration boundaries.
  - Why listed here: Replaces fixed request-level batches with a dynamic batch at every generated token.
- [[paper-vault/papers/kwon2023pagedattention]]
  - Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  - Topic focus: Memory capacity for dynamically changing request batches.
  - Why listed here: Allocates KV state on demand so continuous batching can keep more interleaved sequences resident.

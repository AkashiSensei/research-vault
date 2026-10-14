# LLM Inference Serving

## 2026-08

- [[paper-vault/papers/yu2022orca]]
  - Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  - Scenario focus: Dynamic batching for model-parallel autoregressive inference.
  - Why listed here: Rebuilds the runnable batch at every token iteration and uses selective batching to execute requests with different attention-state lengths.
- [[paper-vault/papers/kwon2023pagedattention]]
  - Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  - Scenario focus: Memory-efficient continuous batching under unpredictable sequence growth.
  - Why listed here: Uses paged KV blocks, sharing, and preemption to increase concurrent request capacity at controlled latency.

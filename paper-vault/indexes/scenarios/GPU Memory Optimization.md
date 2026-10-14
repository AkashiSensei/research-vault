# GPU Memory Optimization

## 2026-05

- [[paper-vault/papers/wang2025jenga]]
  - Title: JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity
  - Scenario focus: Activation and loss-gradient memory during long-context fine-tuning.
  - Why listed here: Reduces the training-side footprint through token elimination, sparsity-aware execution, and segment-based peak cutting.

## 2026-08

- [[paper-vault/papers/kwon2023pagedattention]]
  - Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  - Scenario focus: Dynamically growing KV cache during online autoregressive inference.
  - Why listed here: Replaces contiguous reservations with on-demand blocks and copy-on-write sharing so more live sequences fit in GPU memory.

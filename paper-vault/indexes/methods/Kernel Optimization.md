# Kernel Optimization

## 2026-05

- [[paper-vault/papers/wang2025jenga]]
  - Title: JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity
  - Role in paper: Execution support for JENGA's dynamic token sparsity.
  - Mechanism: Uses permutation-free token movement and segment-based loss processing.
  - Why listed here: Prevents selection overhead and loss-gradient peaks from erasing the end-to-end benefit of token sparsity.

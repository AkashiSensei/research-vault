# Pattern Prediction

## 2026-05

- [[paper-vault/papers/wang2025jenga]]
  - Title: JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity
  - Role in paper: Runtime estimator of token-block importance.
  - Mechanism: Uses lightweight predictors to estimate block importance before the expensive attention computation.
  - Why listed here: Enables early token selection without computing or storing the full attention-score tensor.

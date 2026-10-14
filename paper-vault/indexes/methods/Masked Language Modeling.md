# Masked Language Modeling

## 2026-08

- [[paper-vault/papers/devlin2019bert]]
  - Title: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding
  - Role in paper: Primary token-level pretraining objective.
  - Mechanism: Selects 15% of token positions, replacing most with `[MASK]` while sometimes using a random or unchanged token, then predicts each original identity.
  - Why listed here: Removes the left-to-right training constraint and lets the encoder learn representations conditioned on both preceding and following context.

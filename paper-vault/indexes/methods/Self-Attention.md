# Self-Attention

## 2026-08

- [[paper-vault/papers/vaswani2017attention]]
  - Title: Attention Is All You Need
  - Role in paper: Core token-mixing mechanism in the encoder and decoder.
  - Mechanism: Applies scaled dot-product attention through multiple learned query, key, and value projections, with causal masking in decoder self-attention.
  - Why listed here: Removes recurrent sequence processing, enabling parallel training and short information paths between positions.
- [[paper-vault/papers/devlin2019bert]]
  - Title: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding
  - Role in paper: Bidirectional contextualizer inherited from the Transformer encoder.
  - Mechanism: Lets each token attend to tokens on both sides while masking selected input identities for the pretraining prediction task.
  - Why listed here: Produces deeply context-dependent token representations that can be fine-tuned for both token- and sentence-level predictions.

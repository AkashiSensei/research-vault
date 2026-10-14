# Generative Pre-Training

## 2026-08

- [[paper-vault/papers/radford2018improving]]
  - Title: Improving Language Understanding by Generative Pre-Training
  - Role in paper: Unsupervised representation-learning stage before supervised task adaptation.
  - Mechanism: Trains a causal next-token Transformer on BooksCorpus, then fine-tunes the same parameters on labeled tasks while optionally retaining the language-model objective.
  - Why listed here: Defines the two-stage GPT recipe in which generative language modeling supplies a reusable initialization for discriminative language-understanding tasks.
- [[paper-vault/papers/radford2019language]]
  - Title: Language Models are Unsupervised Multitask Learners
  - Role in paper: Task-agnostic training objective before zero-shot evaluation.
  - Mechanism: Trains a causal next-token model on WebText, then expresses downstream tasks as conditional text generation without new heads or parameter updates.
  - Why listed here: Tests whether scaling one generative objective can itself produce broad multitask behavior, reducing dependence on supervised adaptation.

# Language Model Task Transfer

## 2026-08

- [[paper-vault/papers/radford2018improving]]
  - Title: Improving Language Understanding by Generative Pre-Training
  - Scenario focus: Supervised adaptation of one pretrained causal language model across classification, similarity, entailment, and multiple-choice tasks.
  - Why listed here: Converts heterogeneous task inputs into ordered token sequences, then fine-tunes the shared Transformer and a small task head end to end.
- [[paper-vault/papers/radford2019language]]
  - Title: Language Models are Unsupervised Multitask Learners
  - Scenario focus: Zero-shot execution of language tasks specified through natural-language context.
  - Why listed here: Reframes task transfer as conditional generation at inference time, without task-specific parameter updates or supervised task training.

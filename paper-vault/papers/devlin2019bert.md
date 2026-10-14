---
citekey: devlin2019bert
title: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
authors:
  - Jacob Devlin
  - Ming-Wei Chang
  - Kenton Lee
  - Kristina Toutanova
year: 2019
venue: "NAACL-HLT 2019"
type: paper
status: ingested
tags:
  - natural-language-processing
  - transformers
  - language-model-pretraining
  - transfer-learning
  - self-supervised-learning
  - encoder-only-transformers
topics:
  - Pretrained Language Models
  - Bidirectional Contextual Representations
  - Language Understanding
areas:
  - Natural Language Processing
  - Machine Learning
scenarios:
  - Natural Language Understanding
  - Sequence And Sentence-Pair Classification
  - Extractive Question Answering
  - Token Classification
methods:
  - Transformer Encoder
  - Masked Language Modeling
  - Next Sentence Prediction
  - End-to-End Fine-Tuning
doi: "10.18653/v1/N19-1423"
arxiv: "1810.04805"
official_url: "https://aclanthology.org/N19-1423/"
pdf_url: "https://aclanthology.org/N19-1423.pdf"
code_url: "https://github.com/google-research/bert"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 16-page ACL proceedings paper, including Appendices A-C, was inspected from the verified source artifact."
  - "Title, author order, venue, publication pages, DOI, arXiv identifier, official URLs, and code repository were cross-checked against the ACL paper and its stable records."
  - "The publication year is 2019 even though the first arXiv version appeared in 2018."
  - "Reported leaderboard comparisons are historical snapshots from the paper; they are retained as evidence for the original claim, not as current rankings."
  - "The 93.2 SQuAD v1.1 Test F1 headline uses a seven-model ensemble and TriviaQA augmentation; single-model and no-augmentation results are distinguished below."
created: 2026-08-16
updated: 2026-08-30
---

# BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding

## Core Contribution

> **User-directed revision:** Added a concise statement of BERT's central contribution before the standard summary.

BERT's core contribution is a masked-language-modeling pre-training method that lets the existing Transformer encoder learn deeply bidirectional representations from unlabeled text without seeing the target token. The same pretrained encoder can then be reused across many language-understanding tasks with only small task-specific heads and end-to-end fine-tuning.

## Summary

BERT asks how much task-specific NLP machinery can be replaced by one reusable, deeply bidirectional Transformer representation. It keeps the encoder stack of the original Transformer almost unchanged, exposes every layer to both left and right context, and learns the shared parameters from unlabeled English text before adapting them to supervised language-understanding tasks.

The paper's end-to-end path is:

1. Pack one text span or a pair of spans into a WordPiece sequence using `[CLS]`, `[SEP]`, token embeddings, segment embeddings, and position embeddings.
2. Pass the sequence through a bidirectional Transformer encoder to produce one contextual vector per token plus an aggregate `[CLS]` vector.
3. Pre-train the shared encoder with masked language modeling (MLM) and next sentence prediction (NSP).
4. Initialize a separate downstream model from that checkpoint, add a small task head, and fine-tune all parameters end to end.

This design supports sentence classification through `[CLS]`, token labeling through per-token states, and extractive question answering through start/end span heads without changing the encoder. Across GLUE, SQuAD, SWAG, and CoNLL-2003, the paper combines broad benchmark gains with ablations that attribute much of the advantage to bidirectional MLM, NSP in the evaluated setup, and model scale. The central boundary is equally important: original BERT is an English, encoder-only understanding model with a 512-token maximum sequence and expensive quadratic self-attention, not an autoregressive text generator or a long-context architecture.

## Problem

Earlier contextual representation methods offered two incomplete routes. ELMo supplied separately trained left-to-right and right-to-left LSTM features to task-specific architectures, so the two directions interacted only through a shallow concatenation. OpenAI GPT made transfer simpler by fine-tuning a Transformer with minimal task-specific parameters, but its causal mask prevented every token from conditioning on future context. That restriction is especially awkward for token-level tasks such as extractive question answering, where a token's correct role may depend on words on both sides.

BERT therefore asks whether a single Transformer can be pre-trained with deep bidirectional context and then reused, with only shallow output interfaces, across both sentence-level and token-level language-understanding tasks.

## Research Area And Scenario

- Area(s): Natural Language Processing and Machine Learning.
- Scenario(s): Pre-training reusable language representations, fine-tuning single-sentence and sentence-pair classifiers, extractive question answering, grounded commonsense completion, and token classification.
- Why it matters here: BERT is the representative encoder-only branch of the Transformer lineage. It shows how the original encoder can become a general representation learner rather than remain one half of a supervised sequence-to-sequence model.
- Indexing rationale: The paper belongs under NLP and language-model pre-training because its main contribution is an unlabeled-text pre-training objective and a common fine-tuning interface; it belongs under Transformer architecture because bidirectional encoder self-attention is the mechanism that connects those objectives to diverse downstream heads.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Pre-training uses large monolingual document corpora.
    - Key constraint: **English-only text** from BooksCorpus (800M words) and English Wikipedia (2,500M words).
    - Why it matters: The learned WordPiece vocabulary and contextual statistics are tied to English, while document continuity is required to form NSP examples.
    - Excludes or weakens: Multilingual transfer, non-text modalities, domain-specific terminology absent from those corpora, and claims that the original experiments establish language-agnostic behavior.
  - Claim: Every example is represented as one bounded token sequence.
    - Key constraint: **at most 512 WordPiece tokens**, with one span or two packed spans.
    - Why it matters: Position embeddings and pre-training are learned only to that length, and self-attention cost grows quadratically with sequence length.
    - Excludes or weakens: Documents that require native full-context processing beyond 512 tokens, streaming inputs, and long-context retrieval or generation without chunking or another architecture.
  - Claim: Downstream adaptation uses supervised labels and updates the full encoder.
    - Key constraint: **end-to-end fine-tuning** from one shared checkpoint, normally with a newly initialized shallow task head.
    - Why it matters: BERT is not evaluated as a zero-shot model; its reported task performance includes task data, hyperparameter selection, and optimization of all pretrained weights.
    - Excludes or weakens: Strictly frozen deployment, zero-shot task execution, and settings where a separate model copy per task is too costly.
  - Claim: Sentence-pair pre-training samples retain document relationships.
    - Key constraint: **document-level order**, with 50% true-next and 50% random-second spans for NSP.
    - Why it matters: Shuffled sentence corpora cannot construct the same binary objective, and the paper's NSP ablation is specific to this sampling rule.
    - Excludes or weakens: Corpora without reliable document boundaries and claims that the result establishes every possible discourse-level objective.
- Implied assumptions:
  - Claim: Most target tasks can be expressed through a small interface over `[CLS]` or contextual token states.
    - Key constraint: **classification, tagging, or span selection**, rather than free-form sequence generation.
    - Why it matters: The encoder emits representations but has no autoregressive decoder for producing unconstrained output text.
    - Excludes or weakens: Translation, open-ended summarization, dialogue generation, and other tasks whose native output is a variable-length sequence.
  - Claim: Large-scale pre-training compute is available once and can be amortized across tasks.
    - Key constraint: **TPU-scale pre-training**: 16 TPU chips for BERTBASE and 64 for BERTLARGE, with four days reported for each run.
    - Why it matters: Downstream fine-tuning is comparatively cheap only after the expensive checkpoint exists.
    - Excludes or weakens: Exact from-scratch reproduction under a small compute budget and comparisons that ignore pre-training cost.
- What is ignored or abstracted away:
  - Claim: The evaluation centers on benchmark accuracy rather than deployment behavior.
    - Key constraint: **static 2018-era NLU benchmarks** and aggregate metrics.
    - Why it matters: The paper does not evaluate latency, memory, calibration, adversarial robustness, social bias, privacy, or distribution shift as deployment objectives.
    - Excludes or weakens: Claims that leaderboard gains alone establish safe or reliable production use.
- Applies when:
  - Claim: A task needs rich bidirectional text representations and has enough labeled examples to fine-tune a small output interface.
    - Key constraint: **bounded-text understanding**.
    - Why it matters: In that regime, one pretrained encoder can replace much of a custom task architecture.
- May not apply when:
  - Claim: The output must be generated autoregressively, the decisive evidence lies beyond 512 tokens, or full-model fine-tuning and separate task checkpoints are infeasible.
    - Key constraint: **generation, long context, or constrained adaptation resources**.
    - Why it matters: Those requirements fall outside the original encoder-only interface and evidence.

## Core Idea

BERT turns the Transformer encoder into a reusable language-understanding substrate by separating a shared bidirectional representation learner from thin downstream interfaces. MLM makes deep bidirectionality trainable without allowing a token to copy itself, while NSP gives the aggregate sequence representation a synthetic text-pair objective. The same contextual states can then feed classification, token, or span heads, and all pretrained parameters are adjusted for each target task.

The architectural significance is not a new attention operator. BERT keeps Vaswani et al.'s encoder nearly intact and changes how it is exposed to data: remove causal masking, define a common single/pair input format, pre-train on unlabeled documents, and make the entire encoder the initialization for many supervised models.

## Method

Pre-training and fine-tuning use the same encoder architecture. During pre-training, unlabeled document spans are converted into one shared sequence format, corrupted for MLM, labeled for NSP, and passed through the bidirectional encoder; the two mean losses are summed. During downstream adaptation, the MLM and NSP heads are removed, a task-specific output head is attached to the relevant encoder states, and the complete model is fine-tuned on labeled examples. Each task receives its own fine-tuned parameter copy, even though every copy begins from the same checkpoint.

BERTBASE uses 12 Transformer blocks, hidden size 768, 12 attention heads, and 110M parameters. BERTLARGE uses 24 blocks, hidden size 1024, 16 heads, and 340M parameters. The feed-forward width is `4H`. Pre-training runs for one million steps with batch size 256; 90% of steps use length 128 and the final 10% use length 512 because quadratic attention makes long sequences disproportionately expensive.

- Component: Unified input and embedding interface
  - Role: Represent a single text span or a text pair without changing the encoder.
  - Input: One or two text spans tokenized with a 30,000-item WordPiece vocabulary.
  - Output: One embedding per position, formed by summing token, segment, and position embeddings.
  - Transformation: Prepend `[CLS]`, separate spans and terminate the sequence with `[SEP]`, label tokens as segment A or B, then add an absolute position embedding.
  - Minimal example: A question and passage become `[CLS] question [SEP] passage [SEP]`; segment embeddings identify the question and passage while positions retain their packed order.
- Component: Bidirectional Transformer encoder
  - Role: Produce contextual representations that jointly condition on both sides of every unmasked token.
  - Input: The embedding sequence from the unified interface.
  - Output: A contextual vector `T_i` for every token and an aggregate vector `C` at `[CLS]`.
  - Transformation: Apply a stack of Transformer encoder blocks with unrestricted self-attention over the packed sequence, allowing cross-attention between paired spans inside the same stack.
  - Minimal example: In a packed question-passage sequence, a passage token can attend to the question and to passage words before and after itself at every layer.
- Component: Masked language modeling
  - Role: Train deep bidirectional token representations without exposing the target token unchanged in every case.
  - Input: A WordPiece sequence and a random 15% subset of token positions selected as prediction targets.
  - Output: A vocabulary distribution for each selected position and cross-entropy loss against its original token.
  - Transformation: Replace a selected token with `[MASK]` 80% of the time, a random token 10% of the time, and leave it unchanged 10% of the time; predict the original token in all three cases.
  - Minimal example: If `hairy` is selected in `my dog is hairy`, it normally becomes `[MASK]`, occasionally becomes an unrelated token such as `apple`, and occasionally stays `hairy`, but the target remains `hairy`.
- Component: Next sentence prediction
  - Role: Give the aggregate `[CLS]` representation a pre-training signal about relationships between two text spans.
  - Input: Span A and span B sampled from the corpus.
  - Output: `IsNext` or `NotNext` from `C`, plus a binary classification loss.
  - Transformation: Use the actual following span as B for half of examples and a random corpus span for the other half.
  - Minimal example: A passage followed by its continuation receives `IsNext`; the same first passage paired with an unrelated sentence receives `NotNext`.
- Component: Downstream fine-tuning interfaces
  - Role: Convert shared contextual states into task outputs with minimal new parameters.
  - Input: A pretrained BERT checkpoint, a task-formatted sequence, and supervised labels.
  - Output: A separate end-to-end fine-tuned model for each task.
  - Transformation: Attach a linear classifier to `C` for sequence classification; attach start and end vectors to token states for extractive QA; or attach a token classifier for sequence labeling, then update both the head and all BERT parameters.
  - Minimal example: SQuAD packs question and passage together, scores every passage token with learned start and end vectors, and selects the highest-scoring valid span. SWAG instead packs each candidate continuation separately and compares four scores derived from `[CLS]`.

## Experiments And Evidence

### Core Comparison Map

- **BERT vs. prior representation systems on GLUE → one encoder transfers across diverse sentence and sentence-pair tasks.**
  - Selected evidence: In the paper's Table 1 average, which excludes WNLI, BERTLARGE scores 82.1 versus 75.1 for OpenAI GPT and 74.0 for the pre-OpenAI state of the art; BERTBASE scores 79.6. On MNLI matched/mismatched, BERTLARGE reaches 86.7/85.9 versus GPT's 82.1/81.4. The separately reported official GLUE score is 80.5 versus GPT's 72.8 at the time of writing.
  - Supports: Bidirectional encoder pre-training plus end-to-end fine-tuning generalizes across tasks more effectively than the evaluated causal GPT and ELMo-style baselines.
  - Original: Section 4.1, Table 1, p. 4176; official-score discussion on p. 4176.
- **BERT on SQuAD v1.1 → contextual token states support a minimal extractive span head.**
  - Selected evidence: BERTLARGE single-model performance without stated TriviaQA augmentation is 84.1 EM / 90.9 F1 on Dev. With TriviaQA augmentation it reaches 85.1 EM / 91.8 F1 on Test; the seven-model ensemble with TriviaQA reaches 87.4 / 93.2.
  - Supports: A start/end interface over a pretrained encoder can match or exceed heavily engineered reading-comprehension systems, but the 93.2 headline is not a plain single-model result.
  - Original: Section 4.2, Table 2, p. 4177.
- **BERT on SQuAD v2.0 → the same span interface can include answerability.**
  - Selected evidence: BERTLARGE single reaches 80.0 EM / 83.1 F1 on Test, compared with the prior listed single-system best of 74.8 / 78.0; the paper reports a 5.1 F1 improvement. No TriviaQA data is used for this model.
  - Supports: Treating `[CLS]` as the null span extends the shared QA interface to unanswerable questions without a specialized architecture.
  - Original: Section 4.3, Table 3, p. 4177.
- **BERT vs. GPT and ELMo on SWAG → packed pair encoding transfers to four-way commonsense completion.**
  - Selected evidence: BERTLARGE reaches 86.3 Test accuracy, versus 78.0 for OpenAI GPT and 59.2 for ESIM+ELMo. The paper lists expert human performance as 85.0 and five-annotation human performance as 88.0, both measured on only 100 examples.
  - Supports: Bidirectional pair encoding is useful beyond GLUE and QA, while the small human-evaluation sample makes a broad superhuman claim inappropriate.
  - Original: Section 4.4, Table 4, p. 4177.
- **Full BERT vs. pre-training-objective ablations → MLM bidirectionality and NSP both matter in the paper's setup.**
  - Selected evidence: In Table 5, BERTBASE scores 84.4 MNLI, 88.4 QNLI, 86.7 MRPC, 92.7 SST-2, and 88.5 SQuAD F1. Removing NSP gives 83.9, 84.9, 86.5, 92.6, and 87.9; replacing MLM with a left-to-right LM and omitting NSP gives 82.1, 84.3, 77.5, 92.1, and 77.8. Adding a BiLSTM raises SQuAD to 84.9 but does not recover the bidirectional model.
  - Supports: Deep bidirectional pre-training is especially important for token-level QA and MRPC, while NSP contributes most clearly to QNLI in this comparison.
  - Original: Section 5.1, Table 5, p. 4178.
- **Smaller vs. larger BERT models → scale improves both pre-training fit and small downstream tasks.**
  - Selected evidence: Moving from a 3-layer, 768-hidden model to BERTBASE reduces held-out MLM perplexity from 5.84 to 3.99 and raises MNLI/MRPC/SST-2 Dev accuracy from 77.9/79.8/88.4 to 84.4/86.7/92.9. BERTLARGE reaches perplexity 3.23 and 86.6/87.8/93.7. Table 6 averages five fine-tuning restarts.
  - Supports: With sufficient pre-training, larger representations improve even small labeled tasks rather than only large-scale language modeling.
  - Original: Section 5.2, Table 6, p. 4179.
- **80/10/10 masking vs. alternative replacement mixtures → mixed corruption mainly protects transfer interfaces rather than uniquely maximizing every metric.**
  - Selected evidence: The chosen 80% `[MASK]`, 10% unchanged, 10% random mixture gives 84.2 MNLI, 95.4 fine-tuned NER, and 94.9 feature-based NER. Using 100% `[MASK]` gives 84.3, 94.9, and 94.0; an 80% `[MASK]`/20% unchanged mixture gives 84.4, 95.2, and 94.7.
  - Supports: Fine-tuning is robust to several masking mixtures, while always masking creates a larger pre-train/fine-tune mismatch for frozen feature extraction. The table does not establish 80/10/10 as universally optimal.
  - Original: Appendix C.2, Table 8, p. 4186.

### Supporting Evidence And Boundaries

- Feature extraction remains viable: concatenating BERTBASE's last four hidden layers reaches 96.1 CoNLL-2003 NER Dev F1, only 0.3 below BERTBASE end-to-end fine-tuning at 96.4; BERTLARGE fine-tuning reaches 92.8 Test F1. Original: Section 5.3, Table 7, p. 4179.
- Longer pre-training still helps at the reported budget: one million steps adds almost one MNLI Dev accuracy point over 500,000 steps, and MLM converges slightly more slowly than left-to-right LM training but overtakes it early in absolute accuracy. Original: Appendix C.1, Figure 5, p. 4186.
- The objective ablation is closer to controlled than the headline BERT-vs.-GPT comparison because it fixes BERTBASE data and fine-tuning, but the MLM and left-to-right conditions still differ in corruption, prediction coverage, and attention masking; the results support the complete pre-training configuration rather than a separate causal effect for every difference.
- GLUE learning rates are selected on each Dev set. BERTLARGE is unstable on small datasets, so the authors run several fine-tuning restarts with different data shuffles and classifier initialization and select the best Dev model; Table 6, by contrast, reports five-restart averages.
- The GLUE Test labels are hidden and the paper reports making one server submission. Its leaderboard numbers and the SQuAD leaderboard snapshot are dated to the original study, not present-day rankings.
- The 93.2 SQuAD v1.1 Test F1 result combines a seven-model ensemble, different pre-training checkpoints and fine-tuning seeds, and TriviaQA augmentation. The single-model, no-augmentation Dev result is the cleaner architecture signal.
- Pre-training is substantial: BERTBASE uses 16 TPU chips, BERTLARGE uses 64, and each run takes four days. Fine-tuning can take under an hour on one Cloud TPU, but that does not include checkpoint production.

## Contributions

- Establishes masked language modeling as a practical objective for deeply bidirectional Transformer encoder pre-training.
- Defines one reusable input representation and fine-tuning pattern for single-span, text-pair, token-labeling, and extractive span tasks.
- Demonstrates state-of-the-art results across eleven NLP tasks with minimal task-specific architecture changes.
- Provides controlled objective, architecture-directionality, model-size, masking, and feature-extraction evidence that explains where the gains arise and where they do not.
- Releases pretrained checkpoints and code that made the encoder-only Transformer a reusable community artifact.

## Limitations

- Pre-training and evaluation are overwhelmingly English-only, so the paper does not establish multilingual or language-agnostic transfer.
- The learned positional range and experiments stop at 512 WordPiece tokens. Quadratic self-attention makes longer sequences disproportionately expensive; the paper spends 90% of pre-training steps at length 128 for this reason.
- Original BERT is encoder-only and does not natively perform autoregressive text generation or variable-length sequence-to-sequence decoding.
- Exact pre-training reproduction requires substantial TPU resources and corpus access; four-day runs on 16 or 64 TPU chips are not a low-cost baseline.
- `[MASK]` never appears during fine-tuning. The 80/10/10 procedure mitigates this mismatch, but Table 8 shows it is not uniformly best on every reported metric.
- NSP is a coarse synthetic binary task. The paper's ablation supports it within this data and objective configuration but does not compare later alternatives or establish that next-sentence discrimination is necessary in general.
- Small-task BERTLARGE fine-tuning is unstable. Selecting the best Dev run after several restarts can overstate what one arbitrary training run will achieve.
- Several headline comparisons mix model changes with more pre-training data, larger scale, different objectives, or extra supervised data. The seven-model TriviaQA-augmented SQuAD result should not be read as a matched single-model architecture comparison.
- The benchmark study reports point estimates and historical leaderboard positions, with limited uncertainty analysis and little evidence about domain shift, calibration, robustness, efficiency, bias, privacy, or deployment safety.

## Key Takeaways

- BERT's decisive architectural move is to reuse the Transformer encoder with unrestricted bidirectional self-attention, not to introduce a new attention formula.
- MLM supplies a trainable bidirectional objective; NSP supplies a text-pair signal in this particular setup; shallow task heads expose the resulting shared states.
- The same checkpoint supports sequence classification, token labeling, and span extraction because its input and output interfaces are deliberately unified.
- Broad benchmark gains are strongest when read together with Table 5's directionality/objective ablations and Table 6's scale trend, not as evidence from leaderboard rank alone.
- Result interpretation must separate Dev-selected restarts, single models, extra-data fine-tuning, and ensembles.
- The paper is foundational for bounded-text understanding; generation, long context, multilinguality, and efficient adaptation require later architectural or training extensions.

## Relation: Previous Work

- Title: Attention Is All You Need
  Authors: Ashish Vaswani; Noam Shazeer; Niki Parmar; Jakob Uszkoreit; Llion Jones; Aidan N. Gomez; Łukasz Kaiser; Illia Polosukhin.
  URL: https://papers.nips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html
  Obsidian: [[vaswani2017attention]]
  - Type: uses
  - Status: ingested
  - Work summary: Introduces the Transformer encoder-decoder built from scaled dot-product multi-head attention, position-wise feed-forward networks, positional encodings, residual connections, and layer normalization.
  - Role: Direct architectural substrate for BERT's nearly unchanged encoder stack.
  - Limitation: Its original evaluation trains a supervised encoder-decoder for sequence transduction rather than learning one reusable encoder from unlabeled text.
  - This paper: Retains the encoder, removes the decoder, exposes full bidirectional self-attention, and adds MLM/NSP pre-training plus downstream fine-tuning interfaces.

## Relation: Compared With

- Title: Improving Language Understanding by Generative Pre-Training
  Authors: Alec Radford; Karthik Narasimhan; Tim Salimans; Ilya Sutskever.
  URL: https://openai.com/index/language-unsupervised/
  Obsidian: [[radford2018improving]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Adapts a causal Transformer decoder to generative pre-training on BooksCorpus and then fine-tunes it for diverse language-understanding tasks with task-aware input transformations.
  - Similarity: Both pre-train a Transformer on unlabeled text, reuse pretrained parameters through end-to-end fine-tuning, and minimize task-specific architecture; BERTBASE is sized to make GPT a meaningful reference point.
  - Difference: GPT predicts tokens left-to-right with causal attention, whereas BERT uses an encoder with bidirectional attention, MLM, NSP, segment embeddings, and a larger pre-training corpus.
  - When to use which: BERT is the historical fit for bidirectional representation and token-level understanding; GPT is the architectural ancestor for causal generation and the decoder-only pre-training lineage.
- Title: Deep contextualized word representations
  Authors: Matthew E. Peters; Mark Neumann; Mohit Iyyer; Matt Gardner; Christopher Clark; Kenton Lee; Luke Zettlemoyer.
  URL: https://aclanthology.org/N18-1202/
  - Type: compares-with
  - Status: not-ingested
  - Work summary: ELMo learns contextual word features from separate left-to-right and right-to-left LSTM language models and supplies their representations to task-specific architectures.
  - Similarity: Both use large-scale unlabeled language-model pre-training to produce context-dependent token representations that improve many supervised NLP tasks.
  - Difference: ELMo concatenates independently trained directional features and normally keeps a feature-based task architecture; BERT conditions jointly on both directions at every Transformer layer and primarily fine-tunes the complete encoder.
  - When to use which: ELMo is the historical reference for frozen contextual-feature integration; BERT is preferable when a unified Transformer interface and end-to-end task adaptation are available.

## Source Notes

- Metadata sources:
  - Official ACL Anthology record: https://aclanthology.org/N19-1423/
  - Official ACL Anthology PDF: https://aclanthology.org/N19-1423.pdf
  - DOI record: https://doi.org/10.18653/v1/N19-1423
  - arXiv record: https://arxiv.org/abs/1810.04805
  - Code repository listed by the authors: https://github.com/google-research/bert
- Source verification: A verified source PDF artifact was acquired outside the repository and inspected in full, including the main method, task interfaces, benchmark tables, objective and scale ablations, masking appendix, training details, and relation evidence.
- PDF policy: The PDF was not copied into the vault or repository. Only stable official source URLs are recorded.

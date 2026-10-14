---
citekey: radford2018improving
title: "Improving Language Understanding by Generative Pre-Training"
authors:
  - Alec Radford
  - Karthik Narasimhan
  - Tim Salimans
  - Ilya Sutskever
year: 2018
venue: "OpenAI Technical Report"
type: paper
status: ingested
tags:
  - natural-language-processing
  - transformers
  - language-model-pretraining
  - transfer-learning
  - self-supervised-learning
  - decoder-only-transformers
topics:
  - Pretrained Language Models
  - Generative Pre-Training
  - Language Understanding
areas:
  - Natural Language Processing
  - Machine Learning
scenarios:
  - Natural Language Understanding
  - Text Classification
  - Reading Comprehension
  - Semantic Similarity
  - Multiple-Choice Reasoning
methods:
  - Transformer Decoder
  - Causal Language Modeling
  - Supervised Fine-Tuning
  - Auxiliary Language Modeling
  - Task-Aware Input Transformation
doi: null
arxiv: null
official_url: "https://openai.com/index/language-unsupervised/"
pdf_url: "https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf"
code_url: "https://github.com/openai/finetune-transformer-lm"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete official 12-page technical-report PDF was inspected, including the training framework, task interfaces, experimental tables, ablations, analysis, and references."
  - "The exact title and author order were taken from the PDF; the official OpenAI landing page uses the different headline 'Improving language understanding with unsupervised learning.'"
  - "The PDF labels itself 'Preprint. Work in progress'; OpenAI Technical Report is used as the work type rather than a peer-reviewed venue."
  - "No DOI or arXiv identifier is present in the official materials."
  - "'GPT-1' is a retrospective shorthand; the original paper describes the model as a generative pre-trained Transformer."
created: 2026-08-30
updated: 2026-08-30
---

# Improving Language Understanding by Generative Pre-Training

## Core Contribution

The paper establishes a two-stage transfer-learning recipe in which a causal Transformer is first trained as a language model on unlabeled text and then fine-tuned end to end for supervised language-understanding tasks. It makes one pretrained decoder useful across classification, entailment, similarity, and multiple-choice tasks without redesigning the core architecture for each task.

## Summary

The paper asks how a language model can learn broadly useful representations from abundant unlabeled text and then transfer them to tasks with limited labeled data. Its answer combines a generative objective, which supplies a common learning signal before any target task is known, with discriminative fine-tuning, which specializes the same parameters once task labels become available.

The end-to-end path is:

1. Tokenize long-form BooksCorpus text with byte-pair encoding and train a 12-layer causal Transformer to predict each next token from its left context.
2. Convert each supervised task into an ordered token sequence using delimiter tokens, preserving the pretrained model's single-sequence interface.
3. Add a shallow task output layer and fine-tune all Transformer parameters, optionally retaining the language-model objective as an auxiliary loss.

This design reports state-of-the-art results on nine of twelve evaluated datasets and shows through ablations that pretraining and the Transformer architecture account for most of the aggregate transfer gain. Its scope remains supervised adaptation of a 2018-scale, English, 512-token causal model: it is neither a zero-shot multitask system nor a bidirectional encoder, and its evidence does not establish robust behavior outside the selected benchmarks.

## Problem

Supervised NLP systems traditionally require substantial labeled data and task-specific architectures, while pure language modeling learns from much larger unlabeled corpora without directly optimizing a target task. Earlier transfer methods often use fixed word embeddings or attach pretrained features to separate downstream models, leaving open whether one high-capacity sequence model can learn reusable linguistic knowledge and then adapt with minimal structural changes.

The paper therefore addresses two linked difficulties:

1. How can a pretraining objective learn representations from unlabeled text that transfer across heterogeneous tasks?
2. How can structurally different downstream tasks be represented in a common form so that the same pretrained model can be reused rather than replaced?

## Research Area And Scenario

- Area(s): Natural Language Processing and Machine Learning.
- Scenario(s): Pretraining reusable language models, then fine-tuning them for single-sentence classification, textual entailment, semantic similarity, question answering, commonsense completion, and other multiple-choice reasoning tasks.
- Why it matters here: The paper is the starting point of the decoder-only GPT lineage and an early demonstration that generative Transformer pretraining can replace much task-specific NLP architecture.
- Indexing rationale: It belongs under pretrained language models and transfer learning because the central contribution is a reusable unlabeled-text training stage; it belongs under Transformer methods because causal self-attention is the shared representation mechanism used throughout pretraining and adaptation.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Pretraining draws its transferable signal from a large corpus of continuous text.
    - Key constraint: **English long-form fiction** from BooksCorpus, containing more than 7,000 unpublished books.
    - Why it matters: Long passages provide discourse-level continuity beyond isolated sentences, but their vocabulary, style, and world knowledge shape what the model can learn.
    - Excludes or weakens: Multilingual transfer, non-text modalities, and claims that the reported gains generalize unchanged to technical, conversational, or substantially different domains.
  - Claim: The language model predicts tokens autoregressively inside bounded training sequences.
    - Key constraint: **left-context-only attention over at most 512 tokens**.
    - Why it matters: The causal mask makes next-token prediction well-defined and aligns with generative modeling, but each token representation cannot use future context during pretraining.
    - Excludes or weakens: Native bidirectional encoding and tasks whose decisive evidence lies outside the model's context window.
  - Claim: Every downstream task provides labeled examples and permits updating the pretrained model.
    - Key constraint: **end-to-end supervised fine-tuning**, normally for three epochs, with a newly initialized output layer.
    - Why it matters: The task-specific results combine knowledge from pretraining with direct exposure to target labels and optimization of all model parameters.
    - Excludes or weakens: Zero-shot execution, frozen-feature deployment, and settings where a separate fine-tuned checkpoint per task is infeasible.
- Implied assumptions:
  - Claim: Heterogeneous tasks can be serialized into a form the same decoder can process.
    - Key constraint: **task-aware token ordering and delimiter tokens** followed by a shallow discriminative head.
    - Why it matters: Architecture reuse depends on transforming classification, pairwise, and multiple-choice examples into compatible sequences without losing the relation the label represents.
    - Excludes or weakens: Structured outputs that cannot be expressed through the paper's classification-style heads and input transformations.
  - Claim: Expensive pretraining can be amortized over many downstream tasks.
    - Key constraint: **shared pretraining compute**, reported by OpenAI as roughly one month on eight GPUs.
    - Why it matters: Fine-tuning is comparatively accessible only after the costly reusable checkpoint has been trained.
    - Excludes or weakens: Exact from-scratch reproduction under small compute budgets and comparisons that count downstream cost but ignore pretraining.
- What is ignored or abstracted away:
  - Claim: Evaluation emphasizes aggregate benchmark accuracy rather than deployment reliability.
    - Key constraint: **static English benchmarks and point estimates**.
    - Why it matters: The paper does not directly evaluate calibration, adversarial robustness, social bias, privacy, latency, energy, or uncertainty across repeated training runs.
    - Excludes or weakens: Claims that benchmark transfer alone establishes safe, stable, or efficient production behavior.
- Applies when:
  - Claim: A supervised text task can be serialized into the model's input format and benefits from representations learned from long-form English text.
    - Key constraint: **bounded discriminative language understanding**.
    - Why it matters: In this regime, one pretrained model can replace much of a custom task architecture while adapting its full representation stack.
- May not apply when:
  - Claim: The target requires bidirectional token representations, contexts beyond 512 tokens, no labeled adaptation data, or faithful knowledge outside the pretraining domain.
    - Key constraint: **directionality, context length, supervision, or domain mismatch**.
    - Why it matters: These conditions break or weaken the assumptions behind the original training and evaluation recipe.

## Core Idea

The central observation is that language modeling offers a task-independent objective at the scale of unlabeled text: repeatedly predicting the next token forces a sufficiently capable model to encode syntax, semantics, discourse, and other regularities that later tasks can reuse. Rather than freeze these representations, the paper treats the pretrained parameters as an initialization that supervised learning can reshape for a target decision boundary.

Task diversity is handled at the interface rather than by changing the Transformer. By serializing a document, a pair of sentences, or a context-answer pair into one token stream, the method lets the same causal network produce a final representation for each task; a shallow output layer translates that representation into the required label, while an auxiliary language-model loss can preserve the pretrained behavior during adaptation.

## Method

Training has two stages with one shared parameter stack. Generative pretraining estimates a causal language model over BooksCorpus; supervised fine-tuning then feeds transformed task examples through that model, attaches a task output layer, and updates all parameters. The optional auxiliary language-model objective is optimized alongside the supervised objective during fine-tuning, so transfer is implemented as continued joint optimization rather than as fixed feature extraction.

The base model is a 12-layer Transformer decoder with 768-dimensional states, 12 attention heads, and position-wise feed-forward layers of width 3,072. Pretraining uses 512-token sequences, a 40,000-entry byte-pair-encoding vocabulary, batch size 64, and 100 epochs. The architecture follows the Transformer decoder's masked self-attention while omitting the encoder-decoder cross-attention required for translation.

- Component: BooksCorpus preprocessing
  - Role: Supply continuous unlabeled text for generative pretraining.
  - Input: More than 7,000 unpublished English books with long contiguous passages.
  - Output: Byte-pair-encoded token sequences drawn from a 40,000-entry vocabulary and grouped into spans of up to 512 tokens.
  - Transformation: Segment books into contiguous token sequences while retaining long-range continuity that sentence-shuffled corpora would discard.
  - Minimal example: Consecutive paragraphs from one book remain adjacent in a training span, allowing a later token to be predicted from the preceding narrative context.
- Component: Causal Transformer language model
  - Role: Learn a reusable contextual representation and next-token distribution.
  - Input: A token prefix represented by token and position embeddings.
  - Output: One hidden state per position and a probability distribution for the next token at each training position.
  - Transformation: Apply twelve masked self-attention and feed-forward layers so that position `i` reads positions up to `i` but not future tokens, then project the resulting state to vocabulary logits.
  - Minimal example: To predict the final word in `the quick brown`, the state may attend to `the`, `quick`, and `brown`, but never to the held-out next token.
- Component: Generative pretraining objective
  - Role: Fit all shared Transformer parameters before target-task labels are introduced.
  - Input: Each token sequence and the observed next token following every prefix.
  - Output: The sum of log-likelihood terms for the correct next tokens and a pretrained parameter checkpoint.
  - Transformation: Maximize causal token likelihood across the unlabeled corpus with Adam, a warmup followed by cosine learning-rate decay, and regularization through dropout.
- Component: Task-aware input transformation
  - Role: Express several supervised task structures through the pretrained decoder's single token-sequence interface.
  - Input: A document, sentence pair, or context with candidate answer, plus learned start/end and delimiter tokens.
  - Output: One or more ordered token sequences whose final states can be consumed by a task head.
  - Transformation: Keep a single document as one sequence; concatenate premise and hypothesis for entailment; evaluate both sentence orders for similarity; and concatenate context with each candidate answer for multiple choice.
  - Minimal example: A multiple-choice question with four answers becomes four context-answer sequences, each producing one candidate score that is normalized across the choices.
- Component: Supervised fine-tuning with an auxiliary language-model loss
  - Role: Adapt the shared representation to a target label while optionally retaining the pretraining signal.
  - Input: A pretrained checkpoint, transformed labeled examples, and the task's target classes or candidate choices.
  - Output: A task-specific fine-tuned checkpoint and predictive distribution over labels or choices.
  - Transformation: Add a linear output layer to the final Transformer activation, maximize the supervised log-likelihood, and optionally add the causal language-model objective with weight 0.5; update both the output layer and all Transformer parameters.
  - Minimal example: For entailment, the final state of the concatenated premise-hypothesis sequence is mapped to entailment, neutral, or contradiction probabilities while the auxiliary objective continues predicting tokens in the sequence.

## Experiments And Evidence

- **Generative pretraining vs. training the task model from scratch → pretraining supplies most of the measured transfer benefit.**
  - Selected evidence: Across the analysis tasks summarized by the paper, the full Transformer averages 74.7, while removing pretraining lowers the unweighted average to 59.9, a 14.8-point difference.
  - Supports: The downstream architecture and labels alone do not account for the reported performance; initialization from unlabeled-text language modeling is the dominant intervention in this ablation.
  - Original: Section 5, Table 5, PDF p. 8.
- **Transformer vs. LSTM under the transfer recipe → the attention-based architecture transfers more effectively in the evaluated setup.**
  - Selected evidence: Replacing the Transformer with an LSTM while retaining pretraining and the auxiliary objective gives a 69.1 average, versus 74.7 for the full Transformer.
  - Supports: The gain is not attributable only to access to unlabeled text; model architecture materially affects the quality of transferred representations.
  - Original: Section 5, Table 5, PDF p. 8.
- **Auxiliary language modeling vs. supervised fine-tuning alone → the regularizer has mixed, task-dependent value.**
  - Selected evidence: Removing the auxiliary objective yields a 75.0 unweighted average, slightly above the full model's 74.7, although the paper reports that the auxiliary loss helps larger datasets and can improve convergence.
  - Supports: Continued language modeling is a useful option rather than a uniformly beneficial source of the paper's aggregate gain; its effect should be interpreted by task and data scale.
  - Original: Section 3.2 and Section 5, Table 5, PDF pp. 4 and 8.
- **GPT vs. prior systems on inference, similarity, and multiple-choice benchmarks → one pretrained decoder is competitive across distinct task families.**
  - Selected evidence: The model reports 82.1/81.4 on MultiNLI matched/mismatched, 89.9 on SNLI, 88.3 on SciTail, and 88.1 on QNLI. On Story Cloze it reaches 86.5 versus the listed prior best of 77.6, and on RACE it reaches 59.0 versus 53.3.
  - Supports: The task-input transformations expose several decision structures to one pretrained architecture and support the paper's broad-transfer claim.
  - Original: Section 4.2, Tables 2-3, PDF p. 6.
- **Aggregate gains vs. negative results → the nine-of-twelve headline is broad but not universal.**
  - Selected evidence: The paper reports state of the art on nine of twelve datasets and a GLUE score of 72.8 versus the listed prior result of 68.9; CoLA improves from 35.0 to 45.4. RTE is a counterexample: GPT scores 56.0 versus the listed prior best of 61.7, and MRPC and SST-2 also do not set the best reported score.
  - Supports: Generative pretraining transfers across many tasks, while small datasets and some task structures remain material failure cases rather than exceptions to omit.
  - Original: Abstract; Section 4.2, Tables 2 and 4, PDF pp. 1, 6-7.
- **Evidence boundary.** Results are historical point estimates across heterogeneous baselines, training budgets, and task-specific datasets; the report gives no repeated-seed uncertainty and does not isolate pretraining corpus, compute, architecture, and optimization in a single controlled comparison.

## Contributions

- Defines a practical two-stage recipe that combines causal language-model pretraining on unlabeled text with end-to-end supervised fine-tuning.
- Adapts the Transformer decoder into a reusable general-purpose language representation model without an encoder or encoder-decoder cross-attention.
- Introduces task-aware sequence transformations that let one architecture address classification, entailment, similarity, and multiple-choice tasks through shallow output heads.
- Provides broad benchmark comparisons and ablations separating the effects of pretraining, Transformer architecture, and the auxiliary fine-tuning objective.

## Limitations

- Causal self-attention gives each token only left context during pretraining, so the representation is not deeply bidirectional; this becomes a central contrast with BERT.
- The 512-token context and BooksCorpus domain restrict long-document reasoning, multilingual use, and transfer to styles or knowledge poorly represented by English fiction.
- Every reported downstream task uses labeled data and full-model fine-tuning, so the evidence does not establish zero-shot or frozen-model multitask behavior.
- Pretraining is costly: the official OpenAI account reports roughly one month on eight GPUs, making exact reproduction and controlled scaling expensive.
- Performance is uneven across tasks, including a clear loss to the listed prior result on RTE and no best result on MRPC or SST-2.
- The report provides point estimates without seed variance and compares against systems with unmatched architectures, data, and compute.
- Neither the paper nor its benchmark study evaluates factual reliability, adversarial robustness, social bias, privacy, or deployment safety; OpenAI's accompanying discussion warns that models inherit incomplete, inaccurate, and biased knowledge from their data.

## Key Takeaways

- The paper's durable contribution is the pretrain-then-fine-tune recipe for a decoder-only Transformer, not a new attention operator.
- Causal language modeling provides a scalable unlabeled-data objective, while task-aware serialization and shallow heads make the learned parameters reusable across supervised tasks.
- Table 5 is the strongest causal evidence inside the report: removing pretraining produces the largest measured degradation, and replacing the Transformer with an LSTM also reduces transfer quality.
- The auxiliary language-model loss is not uniformly beneficial, so it should not be treated as the sole explanation for the full model's gains.
- The work is foundational for the GPT lineage, but later models extend its scale and prompting behavior, while BERT directly addresses its left-to-right representation constraint.

## Relation: Previous Work

> **User-directed revision:** Added a compact comparison map connecting the paper's three related-work lines to GPT-1's design response.

| Research line | How it uses unlabeled text | Limitation in the paper's framing | GPT-1's response |
|---|---|---|---|
| Semi-supervised NLP | Computes word- or phrase-level statistics, or supplies pretrained word-, phrase-, or sentence-level representations as features to a supervised model. | Transfers mainly local or fixed representations instead of adapting one shared deep model end to end. | Pretrains and then fine-tunes the complete high-capacity Transformer so higher-level semantics can be learned and adapted across tasks. |
| Unsupervised pre-training | Pretrains a language model, then uses its parameters or hidden states for a supervised target task. | The closest language-model pretraining baselines use LSTMs with shorter effective range and demonstrate a narrower task set; feature-based variants also require substantial new task-specific parameters. | Uses a Transformer for longer-range linguistic structure, evaluates four language-understanding task families, and keeps transfer-time architecture changes small. |
| Auxiliary training objectives | Jointly optimizes an unsupervised objective such as language modeling alongside the supervised target objective. | The unsupervised signal remains coupled to each target-task training process rather than first producing a broadly reusable initialization. | Makes large-scale language-model pretraining an independent first stage, then optionally retains the language-model objective during supervised fine-tuning. |

This table is an analytical synthesis of the three lines organized in Section 2, rather than a claim that every cited predecessor shares one uniform implementation or limitation.

- Title: Attention Is All You Need
  Authors: Ashish Vaswani; Noam Shazeer; Niki Parmar; Jakob Uszkoreit; Llion Jones; Aidan N. Gomez; Łukasz Kaiser; Illia Polosukhin.
  URL: https://papers.nips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html
  Obsidian: [[vaswani2017attention]]
  - Type: uses
  - Status: ingested
  - Work summary: Introduces the Transformer encoder-decoder architecture built from scaled dot-product multi-head attention, position-wise feed-forward networks, positional encodings, residual connections, and layer normalization.
  - Role: Supplies the masked decoder architecture that GPT adapts into a standalone causal language model.
  - Limitation: The original Transformer is trained as a supervised sequence-to-sequence system and does not establish unlabeled generative pretraining as a reusable initialization across understanding tasks.
  - This paper: Removes the encoder and cross-attention path, scales a decoder stack on BooksCorpus, and couples it to task-aware fine-tuning interfaces.

## Relation: Compared With

- Title: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding
  Authors: Jacob Devlin; Ming-Wei Chang; Kenton Lee; Kristina Toutanova.
  URL: https://aclanthology.org/N19-1423/
  Obsidian: [[devlin2019bert]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Pretrains a Transformer encoder with masked language modeling and next sentence prediction, then fine-tunes it through common classification, token, and span interfaces.
  - Similarity: Both learn reusable Transformer parameters from unlabeled English text and adapt the complete model to supervised language-understanding tasks with minimal task-specific architecture.
  - Difference: GPT uses a left-to-right decoder, causal next-token prediction, BooksCorpus, and task-aware serialization; BERT uses bidirectional encoder attention, masked-token and sentence-pair objectives, BooksCorpus plus Wikipedia, and native token-level output states.
  - When to use which: GPT is the historical reference for causal generation and decoder-only pretraining, while BERT is the historical fit for bidirectional bounded-text representation and token-level understanding.

## Source Notes

- Metadata sources:
  - Official OpenAI research page: https://openai.com/index/language-unsupervised/
  - Official OpenAI PDF: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf
  - Authors' code repository: https://github.com/openai/finetune-transformer-lm
- Source verification: The complete official 12-page source artifact was inspected against the title page, method sections, experimental tables, analysis, and references; bibliographic fields were also cross-checked against the official OpenAI page.
- PDF policy: The PDF was not copied into the vault or repository. Only stable official source URLs are recorded.

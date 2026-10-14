---
citekey: radford2019language
title: "Language Models are Unsupervised Multitask Learners"
authors:
  - Alec Radford
  - Jeffrey Wu
  - Rewon Child
  - David Luan
  - Dario Amodei
  - Ilya Sutskever
year: 2019
venue: "OpenAI Technical Report"
type: paper
status: ingested
tags:
  - natural-language-processing
  - language-modeling
  - transformers
  - self-supervised-learning
  - zero-shot-learning
  - decoder-only-transformers
topics:
  - Generative Pre-Training
  - Unsupervised Multitask Learning
  - Zero-Shot Task Transfer
  - Scaling Language Models
areas:
  - Natural Language Processing
  - Machine Learning
scenarios:
  - Zero-Shot Language Task Transfer
  - Open-Ended Text Generation
  - Language Model Evaluation
methods:
  - Autoregressive Language Modeling
  - Decoder-Only Transformer
  - Byte-Level Byte Pair Encoding
  - Web-Scale Pre-Training
doi: null
arxiv: null
official_url: "https://openai.com/index/better-language-models/"
pdf_url: "https://cdn.openai.com/better-language-models/language-models.pdf"
code_url: "https://github.com/openai/gpt-2"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 24-page official technical report was inspected, including the references and appendices."
  - "Title, author order, year, official page, PDF URL, and code repository were cross-checked against OpenAI's primary sources."
  - "No DOI or arXiv identifier is assigned to this report."
  - "The PDF's embedded metadata mentions ICML 2019, but no corresponding official proceedings or DBLP acceptance record was found; the venue is therefore recorded as OpenAI Technical Report, without a peer-review claim."
  - "Table 2 reports 117M, 345M, 762M, and 1542M parameters; later release labels use 124M, 355M, 774M, and 1.5B, so the paper's own counts are retained for its experiments."
created: 2026-08-30
updated: 2026-08-31
---

# Language Models are Unsupervised Multitask Learners

## Core Contribution

GPT-2 scales causal Transformer language modeling to a 40GB web corpus and shows that the resulting model can perform several language tasks from textual context alone, without task-specific parameter updates. The paper reframes sufficiently broad next-token prediction as a potential route to unsupervised multitask learning and supplies early empirical evidence that zero-shot task behavior improves with model capacity.

## Summary

The paper asks whether a language model trained only to predict the next token can infer and execute tasks that occur naturally inside its training text. Instead of collecting labeled examples for each target benchmark, it constructs WebText from millions of outbound Reddit links, trains four decoder-only Transformers up to 1542M parameters, and expresses evaluation tasks as text prefixes or demonstrations.

Its argument proceeds in three stages:

1. Natural text contains both ordinary language and recurring demonstrations of tasks such as question answering, translation, and summarization, so maximizing sequence likelihood may implicitly train a conditional multitask learner.
2. A byte-level BPE vocabulary, a 1024-token context, and a larger GPT-style Transformer make one autoregressive model applicable to varied raw-text domains without task-specific tokenization or architecture changes.
3. Zero-shot performance generally rises with model size: the largest model establishes the reported state of the art on seven of eight language-modeling datasets and displays nontrivial behavior on reading comprehension, summarization, translation, and question answering, although several results remain near weak heuristics or far below supervised systems.

The result is best read as evidence of an emerging training paradigm rather than as a claim that next-token prediction already solves general NLP. Prompt construction, data overlap, model and data scale, and uneven task results all limit causal interpretation, while the paper's own discussion says many practical zero-shot settings remain rudimentary or unusable.

## Problem

The dominant transfer-learning workflow in NLP still required a labeled dataset and supervised adaptation for each target task. Even generative pre-training methods such as GPT-1 learned a reusable language model only as an initialization; they still changed parameters on task-specific examples and often reformatted each task for supervised fine-tuning.

GPT-2 investigates a more ambitious question: can a sufficiently capable language model learn multiple tasks directly from the demonstrations and regularities already present in unlabeled text, then execute those tasks from context without architecture changes or gradient updates? This matters because the cost and narrowness of task-specific datasets constrain how many behaviors can be learned and make systems brittle when task distributions change.

## Research Area And Scenario

- Area(s): Natural Language Processing and Machine Learning, especially autoregressive language modeling, Transformer pre-training, and transfer learning.
- Scenario(s): Zero-shot language-model evaluation, prompt-conditioned reading comprehension, abstractive summarization, translation from textual demonstrations, open-domain question answering, and open-ended text generation.
- Why it matters here: The report is the direct scaled successor to GPT-1 and a foundational demonstration that one decoder-only language model can expose multiple behaviors through textual conditioning rather than supervised task heads.
- Indexing rationale: The paper belongs under language-model pre-training because its central intervention is broad next-token training on WebText; it belongs under zero-shot transfer and decoder-only Transformers because its evidence measures behaviors elicited without downstream parameter updates.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Naturally occurring text contains demonstrations or specifications of many language tasks.
    - Key constraint: **tasks must be representable as text-to-text conditional distributions** inside the model's observed context.
    - Why it matters: The unsupervised-multitask argument depends on next-token prediction encountering enough examples that resemble the desired input, task description, and output relationship.
    - Excludes or weakens: Tasks with non-text actions, hidden state, specialized tools, or output constraints that are not recoverable from textual patterns.
  - Claim: WebText provides broader and cleaner training material than standard language-model corpora.
    - Key constraint: **outbound Reddit links with at least three karma**, followed by heuristic cleaning, deduplication, and Wikipedia removal.
    - Why it matters: This selection yields slightly more than eight million documents and 40GB of text, but its coverage follows the preferences and demographics of Reddit users and linked web publishers.
    - Excludes or weakens: Claims that the corpus is representative of all languages, populations, domains, or communicative settings.
  - Claim: Evaluation uses a fixed pretrained model rather than downstream training.
    - Key constraint: **no target-task parameter or architecture updates**, but task-specific textual framing and decoding rules are still permitted.
    - Why it matters: The reported transfer isolates what can be elicited from the pretrained model, while not eliminating prompt engineering or benchmark-specific post-processing.
    - Excludes or weakens: Equating the paper's protocol with an entirely task-agnostic evaluation pipeline.
  - Claim: Each example fits within the model's learned context interface.
    - Key constraint: **a maximum context of 1024 tokens** and an English-dominant byte-level BPE training distribution.
    - Why it matters: Long documents must be truncated or locally conditioned, and byte-level coverage does not by itself provide balanced multilingual competence.
    - Excludes or weakens: Native long-document reasoning and strong transfer to languages scarcely represented in WebText.
- Implied assumptions:
  - Claim: A larger model's improved zero-shot results reveal better task induction.
    - Key constraint: **capacity, training compute, and optimization behavior change together across the four model sizes**.
    - Why it matters: Figure 1 shows a useful scale trend, but the experiment is not a controlled causal decomposition of which resource produces each behavior.
    - Excludes or weakens: A claim that parameter count alone causes the improvements.
  - Claim: Benchmark test data are sufficiently independent of web pre-training data.
    - Key constraint: **public test text can overlap WebText**, sometimes substantially at the document level.
    - Why it matters: Section 4's 8-gram analysis finds limited average overlap for language-modeling tests but nontrivial overlap for parts of CoQA, so not every score is uncontaminated.
    - Excludes or weakens: Treating all zero-shot results as clean measures of generalization without contamination checks.
- What is ignored or abstracted away:
  - Claim: Aggregate benchmark quality is the primary measure of progress.
    - Key constraint: **point estimates on a small set of 2019-era English-language tasks**, with little uncertainty, robustness, calibration, latency, bias, privacy, or factuality analysis.
    - Why it matters: Emergent benchmark behavior does not establish dependable deployment behavior.
    - Excludes or weakens: Safety, social-impact, and production-readiness claims based only on the reported task scores.
- Applies when:
  - Claim: A target behavior appears frequently enough in broad web text and can be specified or demonstrated inside a 1024-token prompt.
    - Key constraint: **web-like text generation with flexible output evaluation**.
    - Why it matters: In this regime, a single frozen causal model can sometimes replace a separately fine-tuned task model or provide a strong initialization for one.
- May not apply when:
  - Claim: The task requires guaranteed correctness, external retrieval, long context, a low-resource language, precise structured output, or reliable behavior under distribution shift.
    - Key constraint: **capabilities absent from the training objective or evaluation evidence**.
    - Why it matters: The paper's translation, summarization, and open-domain QA results show that recognizable behavior can coexist with large absolute performance gaps and factual errors.

## Core Idea

The key reframing is that language modeling over a sufficiently diverse corpus is not necessarily a single narrow task. Web pages routinely contain questions followed by answers, source text followed by summaries, bilingual pairs, instructions, and other conditional patterns. If a model represents the joint distribution of these sequences, then conditioning on an input plus an implicit or explicit task cue should induce the corresponding output distribution without a new loss or a new parameter set.

GPT-2 realizes this idea by combining a general byte-level text interface with a scaled causal Transformer and broad web pre-training. The mechanism remains ordinary next-token prediction; the novelty lies in treating task demonstrations as part of the context and measuring whether scale turns that objective into observable zero-shot transfer.

## Method

The pipeline first curates WebText, tokenizes every document with a byte-level BPE vocabulary, and trains four GPT-style decoder-only Transformers to maximize autoregressive likelihood. Evaluation then serializes each target task into text, conditions the unchanged model on that text, and scores or decodes the continuation using task-appropriate rules. No target benchmark supplies gradient updates to the model.

The architecture mostly follows GPT-1 but moves layer normalization to the input of each sub-block, adds a final layer normalization, scales residual-path initialization by `1/sqrt(N)` for `N` residual layers, expands the vocabulary to 50,257 tokens, and doubles context length to 1024. Table 2 lists 117M, 345M, 762M, and 1542M-parameter models with 12, 24, 36, and 48 layers and model widths 768, 1024, 1280, and 1600; the smallest matches GPT-1 scale, while the largest is more than an order of magnitude larger.

- Component: WebText corpus construction
  - Role: Supply broad, naturally occurring task demonstrations without using target-benchmark labels.
  - Input: Approximately 45 million outbound links posted to Reddit and receiving at least three karma, with the preliminary collection limited to links before December 2017.
  - Output: Slightly more than eight million deduplicated and heuristically cleaned documents totaling about 40GB of text, with Wikipedia removed to reduce overlap with common benchmarks.
  - Transformation: Resolve linked pages, extract text, remove low-quality or duplicate material, and exclude Wikipedia documents.
  - Minimal example: A positively voted link to an article and its extracted body can enter WebText, while the Reddit discussion and a duplicate copy are not the intended training document.
- Component: Byte-level byte pair encoding
  - Role: Create one reversible vocabulary for arbitrary Unicode strings while retaining useful subword compression.
  - Input: Raw document bytes and corpus-frequency statistics.
  - Output: Token sequences drawn from a 50,257-item vocabulary.
  - Transformation: Begin with 256 byte symbols and learn merges while restricting merges across character categories except for spaces, avoiding a very large base Unicode vocabulary and unknown tokens.
  - Minimal example: An unseen name can still be decomposed into byte-backed units rather than mapped to an out-of-vocabulary symbol.
- Component: Causal Transformer language model
  - Role: Learn a shared conditional distribution over continuations.
  - Input: Up to 1024 prior tokens from a WebText document.
  - Output: A probability distribution over the next token at every position.
  - Transformation: Apply masked multi-head self-attention and feed-forward blocks so each position predicts its successor from left context, then minimize next-token negative log-likelihood.
  - Minimal example: Given a question followed by `A:`, the same next-token mechanism used for ordinary prose assigns probabilities to an answer continuation.
- Component: Textual task conditioning
  - Role: Expose a target task through context without adding a task head or changing model weights.
  - Input: A benchmark example serialized as text, sometimes with a cue or input-output demonstrations.
  - Output: A continuation or likelihood score interpreted by the benchmark protocol.
  - Transformation: Append task-shaped context such as `TL;DR:` for summarization, `Q:` and `A:` fields for CoQA, or English-French example pairs for translation, then generate or score the continuation.
  - Minimal example: For CNN/Daily Mail, append `TL;DR:` to the article, sample 100 tokens with top-`k` sampling at `k=2`, and evaluate the first three generated sentences as the summary.
- Component: Scale comparison
  - Role: Test whether language-model fit and zero-shot task behavior improve with capacity.
  - Input: Four architectures trained on the same corpus and objective.
  - Output: Perplexity, accuracy, F1, BLEU, ROUGE, and exact-match results across language modeling and downstream tasks.
  - Transformation: Evaluate every checkpoint without fine-tuning and compare performance by model size.
  - Minimal example: Figure 1 plots CoQA, WMT-14 French-to-English, CNN/Daily Mail, and Natural Questions performance as the model grows from 117M to 1542M parameters.

## Experiments And Evidence

### Core Comparison Map

- **Four GPT-2 scales across heterogeneous tasks → zero-shot behavior generally strengthens with model capacity.**
  - Selected evidence: Figure 1 shows rising performance from 117M through 1542M parameters on CoQA, WMT-14 French-to-English translation, CNN/Daily Mail summarization, and Natural Questions. Section 3 also reports that held-out WebText perplexity continues to improve with training and that every model remains underfit.
  - Supports: Scale is associated with better language modeling and more visible task behavior under one objective and corpus.
  - Boundary: Architecture depth, width, effective optimization, and compute change together, so the figure does not identify parameter count as the sole cause.
  - Original: Figure 1, p. 2; Sections 2.3 and 3.
- **GPT-2 on eight language-modeling benchmarks → broad zero-shot likelihood transfer, with one conspicuous failure.**
  - Selected evidence: Table 3 reports state-of-the-art results on seven of eight datasets without training or fine-tuning on them. The 1542M model reaches 8.63 perplexity and 63.24 accuracy on LAMBADA, 18.34 perplexity on Penn Treebank, 0.93 BPB on enwik8, 0.98 BPC on text8, and 17.48 perplexity on WikiText-103; on 1 Billion Word it scores 42.16 perplexity versus the listed 21.8 state of the art.
  - Supports: A broad raw-text model transfers well to many test distributions, while destructive preprocessing and sentence shuffling can produce a domain where its reversible raw-text representation is mismatched.
  - Boundary: The paper applies invertible detokenizers that improve perplexity by 2.5 to 5 points; this preserves model parameters but is still task-aware evaluation adaptation.
  - Original: Section 3.1, Table 3, p. 5.
- **GPT-2 on LAMBADA and Winograd Schema Challenge → long-range prediction improves, but evaluation details and sample size matter.**
  - Selected evidence: On LAMBADA, the largest model reaches 8.6 perplexity and 52.66% unconstrained accuracy; filtering stop words raises the reported accuracy to 63.24%, while 19% of gold answers do not appear in the context. On the 273-example Winograd set it reaches 70.70% accuracy, seven percentage points above the prior listed result.
  - Supports: The model often uses broad context and resolves some commonsense references without task training.
  - Boundary: The LAMBADA headline depends on a post-processing rule, and the paper itself urges caution about the tiny Winograd evaluation.
  - Original: Sections 3.3-3.4, Table 3 and Figure 3.
- **GPT-2 on CoQA → frozen generative pre-training yields recognizable reading-comprehension behavior but remains far below supervised BERT.**
  - Selected evidence: Conditioning on the document, conversation history, and an `A:` cue produces 55 F1 on the development set through greedy decoding, matching or exceeding three of four listed supervised baselines trained on more than 127,000 question-answer pairs. The paper contrasts this with supervised BERT systems near 89 F1.
  - Supports: Natural web pre-training can induce a useful question-answering interface without CoQA optimization.
  - Boundary: Qualitative inspection finds frequent name matching and retrieval-like heuristics rather than robust comprehension.
  - Original: Section 3.5.
- **Prompted GPT-2 on CNN/Daily Mail → the cue elicits summarization, but quality barely exceeds a random heuristic.**
  - Selected evidence: Table 4 gives `TL;DR:`-prompted GPT-2 ROUGE-1/2/L scores of 29.34/8.27/26.58, averaging 21.40, compared with 20.98 for Random-3, 31.55 for Lede-3, and 32.75 for the Bottom-Up Summarization system. Removing the hint lowers the average to 15.03.
  - Supports: A short textual cue substantially changes the generated behavior and produces rudimentary summaries.
  - Boundary: The method loses to a lead-sentence heuristic and often confuses specific facts, so the result demonstrates elicitation more strongly than competitive summarization.
  - Original: Section 3.6, Table 4, p. 7.
- **GPT-2 on WMT-14 translation → in-context examples induce direction-dependent translation, not competitive general translation.**
  - Selected evidence: English-to-French reaches about 5 BLEU, below a word-by-word substitution baseline; French-to-English reaches 11.5 BLEU, above several listed unsupervised baselines but far below the best listed unsupervised result of 33.5. The authors detect only about 10MB of French in WebText, roughly 500 times less than a common monolingual training corpus.
  - Supports: A causal language model can infer a translation pattern from textual demonstrations, and data availability strongly shapes the resulting capability.
  - Boundary: The protocol supplies example translation pairs in the context and the absolute quality is poor, so modern terminology would distinguish this from a strict instruction-only zero-shot setting.
  - Original: Section 3.7.
- **GPT-2 on Natural Questions → memorized world knowledge is accessible at high confidence, but aggregate exact match is low.**
  - Selected evidence: Overall exact match is 4.1%; the smallest models remain at or below the paper's roughly 1% most-common-answer-by-type baseline, while the largest model answers 5.3 times as many questions correctly. Accuracy reaches 63.1% for the most confident 1% of questions, yet retrieval-plus-extractive systems cited by the paper score about 30-50% overall. Table 5 shows the 30 most confident generated answers and reports that none of those question strings appeared in WebText under the Section 4 search procedure.
  - Supports: Model scale makes some factual associations directly queryable from parameters.
  - Boundary: Confidence is not broadly calibrated, exact-match performance is far from usable, and the method lacks retrieval or source attribution.
  - Original: Section 3.8, Table 5, pp. 8-9.
- **Training-test overlap analysis → contamination has a measurable but usually limited effect in the tested cases.**
  - Selected evidence: Table 6 finds an average 3.2% WebText overlap for the evaluated language-model test sets versus 5.9% overlap with their own training splits. About 15% of CoQA news documents occur in WebText and score roughly three F1 points higher, producing an estimated overall benefit of 0.5-1.0 F1; removing LAMBADA examples with any detected overlap changes perplexity from 8.6 to 8.7 and accuracy from 63.2% to 62.9%.
  - Supports: Most headline gains are unlikely to be explained solely by exact 8-gram overlap, while some benchmark inflation is present and should be measured.
  - Boundary: N-gram matching cannot detect paraphrases, shorter memorized fragments, or all derivative copies, and CoQA's overlapping articles remain part of the reported aggregate.
  - Original: Section 4, Table 6 and Figure 4, pp. 9-10.

### Interpretation From Sections 5-6

- Section 5 positions GPT-2 as a scale-up of generative pre-training and emphasizes that its transfer protocol changes the central question from how to fine-tune a representation to what behaviors are already latent in the language model.
- Section 6 explicitly limits the result: CoQA is competitive with several supervised baselines, but summarization is rudimentary, many practical tasks are likely near random without sufficient capacity, and the ceiling after supervised fine-tuning is not measured.
- The authors note that BERT demonstrates an efficiency advantage for bidirectional representations on understanding tasks; the report does not establish that scaling a unidirectional model will erase that difference.

## Contributions

> **User-directed revision:** Reframed the contribution list around GPT-2's higher-level influence on general-purpose language modeling rather than benchmark outcomes.

- Adapts byte-level BPE into one reversible vocabulary for arbitrary text, removing unknown-token barriers and making multilingual or cross-lingual use possible at the representation level.
- Introduces WebText, a large and diverse corpus whose naturally occurring text exposes the model to implicit examples of many tasks.
- Shows that next-token prediction can learn context-triggered behaviors such as question answering, translation, and summarization rather than only reusable language representations.
- Expresses task inputs and outputs through text and reuses the language-model output distribution, avoiding task-specific output heads and parameter updates during evaluation.
- Demonstrates that greater model capacity is consistently associated with better language modeling and stronger zero-shot task behavior, indicating substantial remaining scaling headroom.

## Limitations

- WebText is selected through Reddit links receiving at least three karma, which introduces popularity, platform, demographic, language, and web-publishing biases. The paper reports corpus size and collection heuristics but does not provide the document-level transparency needed to characterize or exactly reproduce the distribution.
- The training objective rewards probable continuations, not factual correctness, faithful summarization, calibrated uncertainty, task intent, or harmless behavior. Natural Questions and summarization examples show fluent but incorrect or confused outputs.
- The largest model is evaluated on only a small set of mostly English 2019-era benchmarks. The paper provides little uncertainty analysis and does not systematically test robustness, domain shift, bias, privacy leakage, adversarial prompting, latency, or energy cost.
- The 1024-token context bounds task specifications and document evidence. Long documents are truncated or locally conditioned, and the experiments do not establish long-range retrieval or reasoning beyond that window.
- “Zero-shot” still includes benchmark-specific textual templates, demonstrations, decoding rules, stop-word filtering, and detokenizers. Translation in particular uses input-output examples that later terminology would normally call in-context few-shot prompting.
- Figure 1 confounds parameter count with depth, width, optimization dynamics, and compute. It supports an empirical scaling association but not a causal claim that model size alone produces task learning.
- Web-scale contamination is not fully eliminable. Table 6's 8-gram method finds measurable CoQA overlap and cannot detect all paraphrased or shorter memorized content.
- Several absolute results are weak: prompted summarization barely exceeds Random-3 and trails Lede-3, English-to-French translation loses to a word-substitution baseline, and Natural Questions exact match is only 4.1%.
- The Winograd evaluation contains only 273 examples, and the report offers point estimates rather than confidence intervals; small differences on this dataset are not robust evidence of general commonsense reasoning.
- Training reporting is insufficient for exact independent reproduction: the report gives architecture sizes, batch size, context length, initialization changes, and held-out tuning protocol, but omits a complete optimizer schedule, compute budget, hardware configuration, and corpus artifact.
- All four models remain underfit on WebText, so the experiments do not separate limits of the causal objective from limits of optimization, data, or model scale.
- The technical report is not established as a peer-reviewed ICML publication. Embedded PDF metadata mentioning ICML 2019 is not equivalent to an official proceedings acceptance.
- Paper-era parameter counts and later public-release labels differ: Table 2's 117M/345M/762M/1542M should not be silently replaced by the 124M/355M/774M/1.5B release names when interpreting experimental results.

## Key Takeaways

- GPT-2's durable contribution is the training-paradigm hypothesis that broad causal language modeling can absorb task demonstrations from ordinary text and expose learned behaviors through context.
- The model is a direct scale-up of GPT-1's decoder-only Transformer, but it shifts evaluation from supervised fine-tuning toward frozen, text-conditioned task execution.
- Byte-level BPE, WebText, a 1024-token context, and larger models jointly create the general text interface; the paper does not isolate any one of them as the sole source of transfer.
- The strongest quantitative result is zero-shot language modeling across diverse corpora, while summarization, translation, and factual question answering are early capability probes rather than competitive end systems.
- Figure 1 and Sections 3-4 make scale and contamination central empirical questions that later GPT-family work expands, but both require careful protocol-level interpretation.
- Use the paper's own 117M/345M/762M/1542M counts for its tables and treat later 124M/355M/774M/1.5B names as release-label metadata.

## Relation: Previous Work

- Title: Improving Language Understanding by Generative Pre-Training
  Authors: Alec Radford; Karthik Narasimhan; Tim Salimans; Ilya Sutskever.
  URL: https://openai.com/index/language-unsupervised/
  Obsidian: [[radford2018improving]]
  - Type: extends
  - Status: ingested
  - Work summary: Pre-trains a causal Transformer decoder on BooksCorpus and adapts it to supervised natural-language-understanding tasks through task-aware input transformations and end-to-end fine-tuning.
  - Role: Direct architectural and training-lineage predecessor; GPT-2 retains its decoder-only causal language-model foundation.
  - Limitation: GPT-1 is smaller, uses a narrower book corpus and 512-token context, and requires labeled examples plus parameter updates for every evaluated downstream task.
  - This paper: Scales the model and pre-training distribution, adopts byte-level BPE and a 1024-token context, and tests whether task behavior can be elicited from text without downstream parameter updates.

## Relation: Compared With

- Title: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding
  Authors: Jacob Devlin; Ming-Wei Chang; Kenton Lee; Kristina Toutanova.
  URL: https://aclanthology.org/N19-1423/
  Obsidian: [[devlin2019bert]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Pre-trains a bidirectional Transformer encoder with masked language modeling and next sentence prediction, then fine-tunes the full model with small task heads for supervised language-understanding benchmarks.
  - Similarity: Both papers scale Transformer pre-training on unlabeled text and evaluate whether one shared pretrained model transfers across a broad set of language tasks.
  - Difference: GPT-2 uses a causal decoder and next-token prediction to generate task outputs from context without parameter updates; BERT uses bidirectional encoder representations and supervised fine-tuning for bounded understanding tasks rather than open-ended generation.
  - When to use which: In the papers' historical setting, GPT-2 is the relevant lineage for autoregressive generation and prompt-conditioned task elicitation, while BERT is the stronger fit for bidirectional token or sequence representation when labeled fine-tuning data are available.

## Source Notes

- Metadata sources: OpenAI's official report page, the official PDF, and the OpenAI GPT-2 repository; publication-status checks also considered the official conference record and DBLP.
- Source verification: The complete 24-page official PDF was inspected, with claims traced to Figure 1, Tables 2-6, and Sections 2-6. Metadata and URLs were cross-checked against primary sources.
- Publication status: The artifact is recorded as an OpenAI Technical Report. Embedded PDF metadata mentioning ICML 2019 is retained as a caveat, not interpreted as evidence of peer review or proceedings publication.
- Model naming: Experimental descriptions use Table 2's 117M, 345M, 762M, and 1542M parameter counts. Later public-release labels of 124M, 355M, 774M, and 1.5B are related but not substituted into the report's tables.
- Identifier status: No DOI or arXiv identifier is associated with the report in the verified records.
- PDF policy: The source was inspected without storing a PDF in the public repository; `local_pdf` remains null.

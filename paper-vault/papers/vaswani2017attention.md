---
citekey: vaswani2017attention
title: "Attention Is All You Need"
authors:
  - Ashish Vaswani
  - Noam Shazeer
  - Niki Parmar
  - Jakob Uszkoreit
  - Llion Jones
  - Aidan N. Gomez
  - Łukasz Kaiser
  - Illia Polosukhin
year: 2017
venue: "Advances in Neural Information Processing Systems 30 (NIPS 2017)"
type: paper
status: ingested
tags:
  - transformer
  - attention
  - machine-translation
  - sequence-modeling
  - neural-architecture
topics:
  - Transformer Architecture
  - Sequence Transduction
  - Self-Attention
areas:
  - Machine Learning
  - Natural Language Processing
scenarios:
  - Neural Machine Translation
  - Sequence Transduction
methods:
  - Scaled Dot-Product Attention
  - Multi-Head Attention
  - Positional Encoding
  - Encoder-Decoder Architecture
doi: null
arxiv: "1706.03762"
official_url: "https://papers.nips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html"
pdf_url: "https://papers.nips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf"
code_url: "https://github.com/tensorflow/tensor2tensor"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The title, author order, 2017 conference publication, proceedings volume, and pages 5998-6008 were cross-checked against the official NIPS record, its metadata, DBLP, arXiv, and the proceedings PDF."
  - "The complete 11-page official proceedings PDF was inspected, including the architecture, equations, training setup, Tables 1-3, conclusions, and references."
  - "No conference DOI is recorded by the official proceedings or DBLP; the later DataCite DOI for arXiv:1706.03762 is a preprint identifier and is not stored as a venue DOI."
  - "The official landing-page abstract reports 27.5 English-German and 41.1 English-French BLEU, while the proceedings PDF reports 28.4 and 41.0; the latter values are used throughout this note."
  - "Later arXiv revisions contain content and result wording not present in the inspected conference PDF; the analysis is grounded in the official proceedings version."
created: 2026-08-16
updated: 2026-08-29
---

# Attention Is All You Need

## Summary

The paper asks whether a sequence-to-sequence model needs recurrence or convolution to connect tokens across positions. Its answer is the Transformer: an encoder-decoder architecture whose sequence-mixing operations are attention layers, so all positions within a training example can be processed in parallel instead of being traversed by a recurrent hidden state.

The model's throughline is:

`source tokens -> encoder self-attention -> encoded memory -> decoder masked self-attention + cross-attention -> next-token probabilities`

Scaled dot-product attention turns query-key similarities into weights over values. Multi-head attention repeats that operation in learned representation subspaces, allowing different heads to retrieve different information. Because attention alone does not encode order, sinusoidal position vectors are added to token embeddings. Position-wise feed-forward networks then transform each position independently, while residual connections followed by layer normalization stabilize every sub-layer.

The paper's strongest proceedings evidence is machine translation. On WMT 2014 newstest2014, the big Transformer reports 28.4 BLEU for English-to-German and 41.0 BLEU for English-to-French. Table 3 then varies heads, attention dimensions, model capacity, regularization, and positional representations to show which design choices matter in the reported development setting. The architecture removes recurrence from its layers and parallelizes training across positions; it does not remove the decoder's autoregressive dependency during generation.

## Problem

Recurrent encoder-decoder models process sequence positions through a chain of hidden states. That dependency makes training within one example sequential and creates long computational paths between distant tokens. Convolutional sequence models expose more parallelism, but connecting distant positions still requires multiple layers, with path length determined by kernel width and dilation.

Existing attention mechanisms helped an RNN encoder-decoder select relevant source states, but attention normally remained attached to a recurrent backbone. The paper investigates whether attention can become the primary mechanism for both representation building and source-target interaction, improving training parallelism without sacrificing translation quality.

> **User-directed revision:** Added a direct comparison between recurrent encoder-decoder attention and the Transformer's decoder attention paths.

Concretely, a recurrent decoder carries the target history in its hidden state and uses attention mainly to retrieve relevant encoder states, whereas a Transformer decoder uses masked self-attention to read the target prefix and cross-attention to retrieve from the encoder outputs.

## Research Area And Scenario

- Area(s): Machine Learning and Natural Language Processing.
- Scenario(s): Neural machine translation and general encoder-decoder sequence transduction.
- Why it matters here: The paper specifies the original Transformer interfaces that later encoder-only, decoder-only, and encoder-decoder models adapt: scaled dot-product attention, multiple heads, positional information, residual/post-normalization blocks, and position-wise feed-forward networks.
- Indexing rationale: It is a foundational neural-architecture paper and a primary source for understanding how attention-based sequence models replace recurrent or convolutional sequence mixing.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Each full self-attention layer compares every sequence position with every other permitted position.
    - Key constraint: **quadratic pairwise attention** with per-layer complexity $O(n^2 d)$.
    - Why it matters: Global connectivity gives a constant-length path between positions and allows parallel position processing, but its memory and compute grow quadratically with sequence length.
    - Excludes or weakens: Direct efficiency claims for very long sequences; restricted attention is proposed only as future work.
  - Claim: The decoder predicts a target sequence autoregressively.
    - Key constraint: **causal target order** enforced by shifting target inputs and masking future attention logits.
    - Why it matters: Training can evaluate known target positions in parallel, but inference still needs previously generated tokens before predicting the next one.
    - Excludes or weakens: Claims that the architecture makes sequence generation itself fully non-sequential.
  - Claim: The reported end-to-end evidence uses WMT 2014 English-German and English-French translation.
    - Key constraint: **subword-tokenized bilingual translation** with about 4.5 million and 36 million sentence pairs, respectively.
    - Why it matters: The architecture and optimization choices are tested in large supervised text-to-text workloads rather than arbitrary modalities or low-data domains.
    - Excludes or weakens: Direct empirical claims about language modeling, multimodal inputs, streaming input, or domains outside the two reported translation tasks.
- Implied assumptions:
  - Claim: The practical speed argument assumes hardware can exploit parallel matrix operations across positions.
    - Key constraint: **parallel accelerator execution**, evaluated on eight NVIDIA P100 GPUs.
    - Why it matters: Removing recurrent dependencies creates parallel work, but actual wall-clock gains depend on kernels, memory, and hardware utilization.
    - Excludes or weakens: Direct transfer of the reported training times to serial hardware or substantially different software and accelerator stacks.
  - Claim: Full self-attention is computationally attractive relative to a recurrent layer in the regime emphasized by the paper.
    - Key constraint: **sequence length $n < d$**, where $d$ is representation width.
    - Why it matters: The paper compares $O(n^2d)$ attention with $O(nd^2)$ recurrence; their relative cost changes with sequence length.
    - Excludes or weakens: Treating self-attention as asymptotically cheaper than recurrence for every sequence length.
- What is ignored or abstracted away:
  - Claim: The analytical complexity table abstracts implementation details.
    - Key constraint: **operation-count comparison**, not a measured memory or communication model.
    - Why it matters: Constant sequential depth and short dependency paths do not by themselves quantify memory traffic, accelerator occupancy, or distributed communication.
    - Excludes or weakens: Hardware-independent predictions of latency or energy efficiency.
  - Claim: The reported ablations use one English-German development set.
    - Key constraint: **newstest2013 without reported run variance**.
    - Why it matters: The results locate useful design settings but do not quantify seed sensitivity or establish that every component effect transfers across tasks.
    - Excludes or weakens: Strong causal or universal interpretations of small BLEU differences.
- Applies when:
  - Claim: A task benefits from global, content-dependent interactions across a bounded sequence and from parallel processing during training.
    - Key constraint: **sequence transduction with accessible full context**.
    - Why it matters: Self-attention can expose all positions to one another in one layer while the decoder can separately enforce the target's causal structure.
- May not apply when:
  - Claim: Inputs are extremely long, arrive strictly online, or generation latency is dominated by one-token-at-a-time decoding.
    - Key constraint: **long or streaming sequences**.
    - Why it matters: Full attention becomes expensive, and the original design supplies neither a streaming state mechanism nor a tested sparse-attention solution.

## Core Idea

> **User-directed revision:** Simplified this section to a concise conceptual explanation without equations.

The Transformer replaces sequence-aligned recurrence and convolution with attention as the primary mechanism for exchanging information across positions. Multi-head attention lets each position gather different kinds of context in parallel, while positional encodings preserve order and position-wise feed-forward networks transform the resulting representations. Within the encoder-decoder architecture, this mechanism appears as encoder self-attention, masked decoder self-attention, and encoder-decoder cross-attention.

## Method

> **User-directed revision:** Clarified the semantic input, output, and tensor shape of every major Transformer module, including what each row represents as information flows through the model.

The encoder receives the whole source sequence and produces a contextual memory vector at every source position. Each of six encoder layers applies multi-head self-attention and then a position-wise feed-forward network. The decoder takes the known target prefix, applies masked self-attention, queries the encoder memory through cross-attention, and applies its own feed-forward network. A linear layer and softmax convert the final decoder states to token probabilities.

In the base model, all residual-stream vectors have $d_{model}=512$, the encoder and decoder each have $N=6$ layers, the feed-forward inner width is $d_{ff}=2048$, and eight heads each use $d_k=d_v=64$. Every sub-layer uses the original post-normalization form $\operatorname{LayerNorm}(x+\operatorname{Sublayer}(x))$. During training, the shifted target sequence and causal mask allow all target positions to be evaluated concurrently while preserving the autoregressive conditional factorization.

Dimension convention: Shapes below omit the batch dimension $B$; a batched tensor simply prepends $B$. $L_s$ and $L_t$ are source and target lengths, $L_q$ and $L_k$ are query and key/value lengths, $h$ is the number of heads, and $|\mathcal V|$ is the vocabulary size. Unless stated otherwise, each matrix row represents one sequence position rather than one model parameter.

> **User-directed revision:** Added a concise initialization-based scale-balancing interpretation and evidence boundary for the $\sqrt{d_{model}}$ embedding multiplier.

- Component: Token and positional representation
  - Role: Supply lexical identity and sequence order before the first attention layer.
  - Input: Token IDs $t\in\{1,\ldots,|\mathcal V|\}^{L}$ and positions $0,\ldots,L-1$; each ID names one source token or one shifted-target token.
  - Intermediate: Embedding lookup gives $E\in\mathbb{R}^{L\times d_{model}}$ and the position formula gives $P\in\mathbb{R}^{L\times d_{model}}$; rows $E_i$ and $P_i$ describe the same token position through lexical and positional coordinates respectively.
  - Output: $X\in\mathbb{R}^{L\times d_{model}}$; row $X_i$ is token $i$'s initial model state, combining lexical identity and position but not yet information retrieved from other tokens.
  - Transformation: The model looks up learned token embeddings, scales them by $\sqrt{d_{model}}$, and adds fixed sine/cosine positional encodings whose wavelengths form a geometric progression. Under a common dimension-aware initialization, each embedding coordinate has standard deviation $O(d_{model}^{-1/2})$, equivalently variance $O(1/d_{model})$; multiplying by $\sqrt{d_{model}}$ raises its coordinate scale to $O(1)$, comparable with the fixed sinusoidal coordinates, so the positional signal is less likely to dominate their sum. This scale-balancing rationale is analytical: the paper states the multiplier but neither derives nor ablates its value.
  - Minimal example: Two identical tokens at different positions start with the same word embedding but receive different position vectors, so later attention can distinguish their order.

> **User-directed revision:** Clarified the direct dimensional compatibility conditions among query, key, and value matrices and added a progressive flow checkpoint through one attention head.

- Component: Scaled dot-product attention
  - Role: Retrieve a weighted combination of value vectors for each query.
  - Input: $Q\in\mathbb{R}^{L_q\times d_k}$, $K\in\mathbb{R}^{L_k\times d_k}$, $V\in\mathbb{R}^{L_k\times d_v}$, and an optional $L_q\times L_k$ legality mask. Row $Q_i$ describes what query position $i$ seeks, while $K_j$ describes when position $j$ should match and $V_j$ is the content returned if it is selected. $Q$ and $K$ must share feature width $d_k$; $K$ and $V$ must share the same $L_k$ positions.
  - Intermediate: $S=QK^\top/\sqrt{d_k}\in\mathbb{R}^{L_q\times L_k}$ contains one score for every query-key pair, and $A=\operatorname{softmax}(S)\in\mathbb{R}^{L_q\times L_k}$ contains row-normalized attention weights; $A_{ij}$ says how much query position $i$ reads from key/value position $j$.
  - Output: $Z=AV\in\mathbb{R}^{L_q\times d_v}$; row $Z_i$ is one query position's retrieved context summary, formed by weighting all legal value rows. It is a single-head result, not yet the final $d_{model}$-wide sub-layer output.
  - Transformation: Compute the pairwise scores, apply the mask by replacing forbidden logits with $-\infty$, normalize each query row with softmax, and use that row as weights over $V$.
  - Minimal example: In encoder self-attention, $Z_i$ may combine values from every source position; in decoder self-attention, future positions $j>i$ are masked so $Z_i$ uses only the known prefix.

Main flow so far — token representation through one attention head:

$$
E \xrightarrow{\times\sqrt{d_{model}}} \sqrt{d_{model}}E \xrightarrow{+P} X \xrightarrow{W^Q,W^K,W^V} (Q,K,V) \xrightarrow{QK^\top/\sqrt{d_k}} S \xrightarrow{\operatorname{softmax}} A \xrightarrow{\times V} Z
$$

Here $E$ is the learned token embedding, $P$ is the positional encoding, $S$ is the scaled score matrix, $A$ is the normalized attention-weight matrix, and $Z$ is the value-weighted output. A legality mask, when needed, is applied to $S$ before softmax.

> **User-directed revision:** Clarified the single-head aggregation bottleneck and distinguished the general multi-head output-mapping requirement from the paper's concatenate-then-project implementation.

- Component: Multi-head attention
  - Role: Preserve several independently weighted context summaries instead of forcing different relevant positions and relations into one aggregate, then combine them into one $d_{model}$-dimensional vector that can re-enter the residual stream. A single head's weighted sum can retain combined evidence, but it can make separate sources and roles harder for later layers to distinguish.
  - Input: Query-side states $X_Q\in\mathbb{R}^{L_q\times d_{model}}$ and key/value-side states $X_{KV}\in\mathbb{R}^{L_k\times d_{model}}$. In self-attention they are the same sequence ($X_Q=X_{KV}=X$); in cross-attention, $X_Q$ is the decoder state sequence and $X_{KV}$ is the encoder memory sequence.
  - Intermediate: Head $i$ uses $W_i^Q,W_i^K\in\mathbb{R}^{d_{model}\times d_k}$ and $W_i^V\in\mathbb{R}^{d_{model}\times d_v}$ to form $Q_i=X_QW_i^Q\in\mathbb{R}^{L_q\times d_k}$, $K_i=X_{KV}W_i^K\in\mathbb{R}^{L_k\times d_k}$, and $V_i=X_{KV}W_i^V\in\mathbb{R}^{L_k\times d_v}$, then returns $H_i\in\mathbb{R}^{L_q\times d_v}$. Row $(H_i)_r$ is head $i$'s context summary for query position $r$.
  - Output: $M\in\mathbb{R}^{L_q\times d_{model}}$; row $M_r$ is the combined multi-head contextual update for query position $r$, aligned with the residual row $(X_Q)_r$ to which it will be added.
  - Transformation: The general interface requires an aggregation map $F:(\mathbb{R}^{d_v})^h\rightarrow\mathbb{R}^{d_{model}}$ at each query position; concatenation itself is not mathematically required. The paper concatenates $H_1,\ldots,H_h$ into an $L_q\times hd_v$ matrix and applies $W^O\in\mathbb{R}^{hd_v\times d_{model}}$. In the base model, $h=8$ and $d_v=64$, so the concatenated width is already $512=d_{model}$, but $W^O$ still learns cross-head mixtures. Partitioning $W^O$ into blocks gives $\operatorname{Concat}(H_1,\ldots,H_h)W^O=\sum_i H_iW_i^O$, showing that concatenate-then-project is the paper's linear parameterization rather than the only possible combiner.
  - Minimal example: A subject-oriented head and a location-oriented head can retain separate summaries before recombination; the paper places them in separate coordinate blocks and lets $W^O$ mix them, while another architecture could use a different map $F$ as long as it returns one $d_{model}$-dimensional vector per query position.

> **User-directed revision:** Relocated the Add & Norm flow checkpoint, separated the wrapper from the feed-forward network, and clarified their tensor interfaces and dropout behavior.

- Component: Residual Add and LayerNorm wrapper
  - Role: Preserve the sub-layer input as a direct information path, add the learned update, and standardize each position's feature scale before the next sub-layer.
  - Input: Residual states $X\in\mathbb{R}^{L\times d_{model}}$ and a same-shaped sub-layer update $M\in\mathbb{R}^{L\times d_{model}}$; rows $X_i$ and $M_i$ refer to the same token/query position.
  - Output: $Y\in\mathbb{R}^{L\times d_{model}}$; row $Y_i$ is the same position's prior state plus its new contextual or feed-forward update, normalized across its $d_{model}$ features.
  - Transformation: During training, form $R=X+\operatorname{Dropout}(M)$; during inference, dropout is the identity. The original paper then applies post-normalization independently to each row: $Y_i=\gamma\odot(R_i-\mu_i)/\sqrt{\sigma_i^2+\epsilon}+\beta$, where $\mu_i$ and $\sigma_i^2$ come from the current row and $\gamma,\beta\in\mathbb{R}^{d_{model}}$ are learned.
  - Minimal example: If an attention update is near zero, the residual branch still carries the token's previous representation forward before LayerNorm; Add itself has no learned parameters.

Main flow so far — complete encoder self-attention sub-layer through its first Add & Norm:

$$
X \rightarrow Q,K,V \rightarrow \text{Attention in each head} \rightarrow \operatorname{Concat} \rightarrow W^O \rightarrow \operatorname{Dropout} \rightarrow \text{Residual Add} \rightarrow \operatorname{LayerNorm} \rightarrow Y
$$

The compact path corresponds to $M=\operatorname{Concat}(H_1,\ldots,H_h)W^O$, $R=X+\operatorname{Dropout}(M)$, and $Y=\operatorname{LayerNorm}(R)$. Each row keeps the same query position throughout: $M_i$ is its contextual update, $R_i$ adds back its prior state $X_i$, and $Y_i$ is the normalized state passed to the feed-forward sub-layer.

During training, dropout zeros activation coordinates with probability $p$ and rescales retained values by $1/(1-p)$; the base model uses $p=0.1$. During inference, it is the identity—no masking or rescaling.

- Component: Position-wise feed-forward network
  - Role: Apply a learned nonlinear feature transformation to each token independently after attention has exchanged information across positions.
  - Input: $Y\in\mathbb{R}^{L\times d_{model}}$; row $Y_i$ is token position $i$'s attention-enriched, normalized state.
  - Intermediate: $U=\max(0,YW_1+b_1)\in\mathbb{R}^{L\times d_{ff}}$; row $U_i$ expands that same token into a wider nonlinear feature space, with no information exchanged between different rows.
  - Output: The FFN returns $F=UW_2+b_2\in\mathbb{R}^{L\times d_{model}}$; row $F_i$ is a feature update for token $i$. Applying the same Add & Norm wrapper yields $X_{next}\in\mathbb{R}^{L\times d_{model}}$, the representation passed to the next encoder layer.
  - Transformation: $W_1\in\mathbb{R}^{d_{model}\times d_{ff}}$ expands each row, and $W_2\in\mathbb{R}^{d_{ff}\times d_{model}}$ projects it back. In the base model this is $512\rightarrow2048\rightarrow512$ with ReLU between the projections; the same weights are reused at every sequence position.
  - Minimal example: Attention may bring subject information into a verb's row; the FFN then transforms the features inside that verb row without reading another position directly.

Main flow so far — complete encoder layer:

$$
Y \rightarrow \text{Linear }(d_{model}\!\rightarrow\!d_{ff}) \rightarrow \operatorname{ReLU} \rightarrow \text{Linear }(d_{ff}\!\rightarrow\!d_{model}) \rightarrow \operatorname{Dropout} \rightarrow \text{Residual Add }(+Y) \rightarrow \operatorname{LayerNorm} \rightarrow X_{next}
$$

- Component: Encoder stack
  - Role: Turn source-token representations into contextual memory for the decoder.
  - Input: $X_{enc}^{(0)}\in\mathbb{R}^{L_s\times d_{model}}$ from the source token and positional representation; row $(X_{enc}^{(0)})_i$ initially identifies source token $i$ and its position.
  - Output: $E_{enc}\in\mathbb{R}^{L_s\times d_{model}}$ after six layers. There are still $L_s$ rows: row $(E_{enc})_i$ remains aligned with source position $i$, but now represents that token in the context of the whole source sentence. The decoder later treats these rows as its searchable source memory.
  - Transformation: Every encoder layer maps $L_s\times d_{model}$ states to the same shape. Unmasked self-attention creates one contextual update for each of the $L_s$ source queries by reading all $L_s$ source key/value rows; Add & Norm merges those updates with the previous states, and the position-wise FFN transforms each resulting row before a second Add & Norm.
  - Minimal example: The output row aligned with a source verb can contain evidence retrieved from a distant subject and object while remaining the memory row for that verb.
- Component: Decoder stack
  - Role: Combine the target prefix with encoded source memory to form the state used for the next-token distribution.
  - Input: Shifted-target states $X_{dec}^{(0)}\in\mathbb{R}^{L_t\times d_{model}}$ and encoder memory $E_{enc}\in\mathbb{R}^{L_s\times d_{model}}$. Row $(X_{dec}^{(0)})_i$ represents the known target token supplied at decoder position $i$; the shift ensures it does not supply the answer that position is trained to predict.
  - Output: $D\in\mathbb{R}^{L_t\times d_{model}}$ after six layers. Row $D_i$ is the final state for target position $i$: it summarizes the legally visible target prefix and source information retrieved for that position, and is the row used to predict that position's target token.
  - Transformation: Each layer preserves the $L_t\times d_{model}$ decoder shape. Masked self-attention produces one prefix-aware result for each target query while forbidding future target keys. Cross-attention then projects the $L_t$ decoder rows into queries $Q\in\mathbb{R}^{L_t\times d_k}$ and the $L_s$ encoder-memory rows into keys $K\in\mathbb{R}^{L_s\times d_k}$ and values $V\in\mathbb{R}^{L_s\times d_v}$ per head, so its $L_t\times L_s$ weight matrix lets every target position retrieve a different weighted summary of the source. A position-wise FFN follows; Add & Norm wraps all three sub-layers.
  - Minimal example: The decoder row used to predict a translated verb can summarize the preceding target words while assigning high cross-attention weight to the source verb and its subject.
- Component: Output and training procedure
  - Role: Learn the encoder-decoder parameters and turn decoder states into vocabulary predictions.
  - Input: Final decoder states $D\in\mathbb{R}^{L_t\times d_{model}}$; row $D_i$ is the contextual evidence from which the model must choose the target token at position $i$. Training additionally supplies the correct token ID for every such row.
  - Intermediate: With the tied embedding matrix $W_E\in\mathbb{R}^{|\mathcal V|\times d_{model}}$, the pre-softmax projection produces logits $G=DW_E^\top\in\mathbb{R}^{L_t\times|\mathcal V|}$, where $G_{i,v}$ is the unnormalized score assigned by target-position state $D_i$ to vocabulary token $v$.
  - Output: $P=\operatorname{softmax}(G)\in\mathbb{R}^{L_t\times|\mathcal V|}$; row $P_i$ is one probability distribution over the vocabulary for target position $i$, not a representation passed to another Transformer layer. During inference, the selected next token is appended to the known prefix and the decoder is run again for the next position.
  - Transformation: The source embedding, target embedding, and pre-softmax projection share $W_E$. Training compares every probability row with its correct target token using label-smoothed cross-entropy and updates all learned parameters with Adam ($\beta_1=0.9$, $\beta_2=0.98$, $\epsilon=10^{-9}$) under a 4,000-step warmup and inverse-square-root decay schedule.
  - Minimal example: If $L_t=10$ and $|\mathcal V|=37{,}000$, the decoder returns ten vocabulary distributions, one for each training position; it does not return one distribution for the sentence as a whole.

Main flow so far — end-to-end tensor interfaces:

$$
\text{source IDs }[L_s] \rightarrow X_{enc}^{(0)}[L_s,d_{model}] \rightarrow E_{enc}[L_s,d_{model}],
$$

$$
\text{shifted target IDs }[L_t] \rightarrow X_{dec}^{(0)}[L_t,d_{model}] \xrightarrow[\text{read }E_{enc}]{\text{masked self-attention + cross-attention + FFN}} D[L_t,d_{model}] \rightarrow G[L_t,|\mathcal V|] \rightarrow P[L_t,|\mathcal V|].
$$

The first axis always enumerates sequence positions: encoder row $i$ is the evolving representation of source position $i$, decoder row $j$ is the evolving representation used at target position $j$, and probability row $j$ is that target position's distribution over vocabulary choices.

## Complete Encoder Flow: Source Tokens To Encoder Memory

> **User-directed revision:** Promoted this walkthrough to a top-level section and expanded it into the complete encoder flow, including both Add & Norm stages, the position-wise feed-forward network, and repetition across encoder layers.

1. For a source of length $L_s$, tokenization replaces the sentence with vocabulary IDs $t=(t_1,\ldots,t_{L_s})$; these are discrete identifiers rather than semantic vectors.
2. Embedding lookup produces $E\in\mathbb{R}^{L_s\times d_{model}}$, and positional encoding produces a same-shaped $P$. After scaling the embeddings, adding position information, and applying input dropout during training, denote the first encoder input by $X^{(0)}\in\mathbb{R}^{L_s\times d_{model}}$; row $X_i^{(0)}$ combines token $i$'s identity and position.
3. In attention head $r$, separate learned projections turn $X^{(0)}$ into $Q_r,K_r\in\mathbb{R}^{L_s\times d_k}$ and $V_r\in\mathbb{R}^{L_s\times d_v}$. Their rows respectively describe what each position seeks, when it should be selected, and what content it can contribute.
4. Comparing every query row with every key row produces a score matrix $S_r\in\mathbb{R}^{L_s\times L_s}$; softmax turns it into a weight matrix $A_r$ of the same shape. Row $i$ is token $i$'s reading distribution, and column $j$ refers to source token $j$.
5. Applying $A_r$ to the value rows produces the head result $Z_r\in\mathbb{R}^{L_s\times d_v}$. Row $(Z_r)_i$ is the context collected for query position $i$, so the head still returns one row per original token rather than collapsing the sentence.
6. Concatenating the same-position rows from all heads gives $C\in\mathbb{R}^{L_s\times D_{heads}}$, where $D_{heads}$ is their total feature width and need not equal $d_{model}$.
7. The learned output projection $W^O\in\mathbb{R}^{D_{heads}\times d_{model}}$ mixes the head features, giving $M=CW^O\in\mathbb{R}^{L_s\times d_{model}}$. Row $M_i$ is the attention update aligned with original input row $X_i^{(0)}$.
8. Training-time dropout turns $M$ into a same-shaped update $\widetilde M$; at inference, $\widetilde M=M$. The first residual sum $R_{attn}^{(1)}=X^{(0)}+\widetilde M$ therefore preserves $L_s$ token rows and the $d_{model}$ width while adding contextual information.
9. The first LayerNorm independently normalizes the features inside each row of $R_{attn}^{(1)}$, using that token's own mean and variance but one learned pair $\gamma_{attn}^{(1)},\beta_{attn}^{(1)}$ shared across token positions. Its output $Y^{(1)}\in\mathbb{R}^{L_s\times d_{model}}$ is the attention-enriched state passed to the feed-forward sub-layer.
10. The position-wise feed-forward network applies the same learned two-layer transformation to every row of $Y^{(1)}$ independently: it expands the feature width from $d_{model}$ to $d_{ff}$, applies ReLU, and projects back to $d_{model}$. The result $F^{(1)}\in\mathbb{R}^{L_s\times d_{model}}$ is one feature update per token; this sub-layer does not mix different token rows.
11. Dropout is applied to $F^{(1)}$, the second residual connection adds that update back to $Y^{(1)}$, and the second LayerNorm returns the first encoder layer's output $X^{(1)}\in\mathbb{R}^{L_s\times d_{model}}$. This LayerNorm has its own learned $\gamma_{ffn}^{(1)},\beta_{ffn}^{(1)}$, separate from the first LayerNorm's parameters, although all parameters are optimized together.
12. The base encoder repeats this complete layer six times. Layer $\ell$ takes $X^{(\ell-1)}$ as its input and produces $X^{(\ell)}$ through self-attention, Add & Norm, the position-wise FFN, and a second Add & Norm; positional encoding is not added again, and every layer has its own attention projections, output projection, FFN weights, and LayerNorm parameters. The final $E_{enc}=X^{(6)}$ retains one row per source position, now contextualized through all six layers, and becomes the source memory supplied to the decoder.

## Complete Decoder Flow: Target Prefix To Token Probabilities

> **User-directed revision:** Simplified this top-level section into one continuous decoder execution flow, followed by a brief contrast between autoregressive inference and parallel teacher-forced training.

1. The source sequence first completes the encoder flow, producing the fixed encoder memory $E_{enc}\in\mathbb{R}^{L_s\times d_{model}}$ with one contextualized row per source token; the decoder reads this memory but does not change it.
2. The decoder receives a shifted target prefix of length $L_t$, looks up its token embeddings, adds target positional encodings, and forms $D^{(0)}\in\mathbb{R}^{L_t\times d_{model}}$. Row $j$ is the decoder's initial representation of the token placed at target position $j$.
3. In decoder layer $\ell$, each masked-self-attention head projects $D^{(\ell-1)}$ into its own $Q_{self}^{(\ell)},K_{self}^{(\ell)},V_{self}^{(\ell)}$; these describe what every target position seeks, when it should be selected, and what content it can contribute.
4. Each target query is compared with the target keys, but the causal mask removes future positions before softmax. The resulting weights let row $j$ combine only value rows $1$ through $j$, so every position becomes aware of the target prefix available to it.
5. The self-attention heads return one result row per target position; their rows are concatenated and mapped through the learned output projection to $d_{model}$. Dropout, residual addition with $D^{(\ell-1)}$, and the first LayerNorm produce the prefix-aware state $S^{(\ell)}\in\mathbb{R}^{L_t\times d_{model}}$.
6. In cross-attention, each head forms queries from $S^{(\ell)}$ and forms keys and values by projecting the fixed encoder memory $E_{enc}$ with this decoder layer's own learned matrices. Every target row can therefore query all $L_s$ source rows, producing one source-context result per target position.
7. The cross-attention heads are concatenated and projected back to $d_{model}$. Dropout, residual addition with $S^{(\ell)}$, and the second LayerNorm produce the source-aware target state $C^{(\ell)}\in\mathbb{R}^{L_t\times d_{model}}$.
8. The position-wise feed-forward network transforms every row of $C^{(\ell)}$ independently, using the same FFN parameters across target positions. Dropout, residual addition with $C^{(\ell)}$, and the third LayerNorm produce the decoder-layer output $D^{(\ell)}\in\mathbb{R}^{L_t\times d_{model}}$.
9. The base decoder repeats this complete layer six times. Layer $\ell$ receives $D^{(\ell-1)}$, while every layer has independent attention projections, FFN weights, and LayerNorm parameters; the encoder memory remains the same, although each decoder layer projects it into its own cross-attention keys and values.
10. The final decoder states $D^{(6)}$ are mapped through the tied vocabulary projection and softmax to $P\in\mathbb{R}^{L_t\times |\mathcal V|}$. Probability row $j$ predicts the target token that follows the input at position $j$.
11. During autoregressive inference, the first input is BOS and only the final probability row is used to select $y_1$; then $y_1$ is appended and used to select $y_2$, continuing until EOS. A selected token receives its embedding and QKV only when it is fed back on the next step, and a KV cache can reuse earlier target keys and values while advancing only the new position.

Training follows the same decoder-layer operations but supplies the complete correct target sequence shifted right, computes all target positions in parallel under the causal mask, keeps dropout active, and backpropagates the loss to update the model. Inference instead feeds back model-selected tokens one position at a time, keeps parameters fixed, disables dropout, and may use KV caches to avoid recomputing earlier positions.

## Experiments And Evidence

- **Self-attention vs. recurrent and convolutional layers -> shorter dependency paths and more parallel position processing.**
  - Selected evidence: Table 1 assigns full self-attention $O(n^2d)$ work, $O(1)$ sequential operations, and $O(1)$ maximum path length, compared with recurrent layers at $O(nd^2)$, $O(n)$, and $O(n)$. The paper explicitly limits the computational advantage over recurrence to the common translation regime $n<d$.
  - Supports: Removing sequence-aligned recurrence exposes within-example parallelism and directly connects distant positions, subject to the quadratic full-attention boundary.
  - Original: Section 4 and Table 1, proceedings pages 6002-6003 (PDF pages 5-6).
- **Transformer vs. contemporary translation systems -> stronger reported newstest2014 BLEU with lower estimated training cost in the evaluated setup.**
  - Selected evidence: The big Transformer reports **28.4 English-German BLEU**, exceeding the listed best ensemble at 26.36, and **41.0 English-French BLEU**, exceeding every listed single model but not the ConvS2S ensemble at 41.29. The base Transformer reports 27.3 and 38.1. For English-German, the reported estimated training costs are $3.3\times10^{18}$ FLOPs for base and $2.3\times10^{19}$ for big, versus $9.6\times10^{18}$ for the listed ConvS2S single model at 25.16 BLEU.
  - Supports: In these two translation benchmarks, the architecture can improve quality while using substantially less reported training computation than several recurrent or convolutional competitors. The English-French claim is specifically single-model state of the art.
  - Original: Section 6.1 and Table 2, proceedings page 6005 (PDF page 8).
- **Head count and head dimension variants -> several smaller attention subspaces outperform one full-width head, but more heads are not monotonically better.**
  - Selected evidence: Table 3 reports 24.9 development BLEU for one 512-dimensional head, 25.8 for the eight-head base model, 25.8 for 16 heads, and 25.4 for 32 heads. These rows jointly change head count and per-head key/value dimensions to keep computation approximately constant. Separate rows reducing $d_k$ to 16 and 32 report 25.1 and 25.4, versus 25.8 for $d_k=64$ in the base model.
  - Supports: Multiple learned subspaces and sufficient key dimension help in the reported setting; the evidence does not isolate head count from every coupled dimension or establish that adding heads always helps.
  - Original: Section 6.2 and Table 3, rows A-B, proceedings pages 6005-6006 (PDF pages 8-9).
- **Depth and width variants -> model capacity matters, but the ablation does not attribute the gain to one universal scaling rule.**
  - Selected evidence: A two-layer variant reports 23.7 development BLEU versus 25.8 for the six-layer base; a $d_{model}=1024$ variant reports 26.0, and a $d_{ff}=4096$ variant reports 26.2. The complete big configuration reaches 26.4 with 213 million parameters after 300,000 steps.
  - Supports: Shallower/narrower variants lose quality and added capacity can improve it, while several dimensions and the training budget differ across the full set of rows.
  - Original: Section 6.2 and Table 3, row C and the big-model row, proceedings page 6006 (PDF page 9).
- **Regularization variants -> dropout and label smoothing are part of the effective architecture/training recipe.**
  - Selected evidence: Removing residual dropout lowers development BLEU from the base model's 25.8 to 24.6; dropout 0.2 gives 25.5. Removing label smoothing gives 25.3, while 0.2 gives 25.7 compared with the base value 0.1.
  - Supports: The reported gains cannot be assigned to attention alone; the surrounding regularization and optimization recipe materially affects translation quality.
  - Original: Section 6.2 and Table 3, row D, proceedings page 6006 (PDF page 9).
- **Sinusoidal vs. learned positions -> nearly identical observed quality in this test.**
  - Selected evidence: Replacing sinusoidal encodings with learned positional embeddings gives 25.7 development BLEU versus 25.8 for the base model.
  - Supports: The experiment supports the need for positional information but does not show that the sinusoidal form is responsible for the model's main gain or demonstrate the proposed extrapolation benefit.
  - Original: Section 3.5 and Table 3, row E, proceedings pages 6002 and 6006 (PDF pages 5 and 9).
- **Evidence boundary.** Table 3 uses WMT English-German newstest2013, no checkpoint averaging, and reports no seed variance. Table 2 uses checkpoint averaging, beam size 4, length penalty 0.6, and development-selected decoding settings. Training FLOPs are estimates computed from time, GPU count, and assumed sustained single-precision throughput rather than direct measurements.

## Contributions

- Introduces the Transformer as an encoder-decoder sequence-transduction model without sequence-aligned recurrence or convolution.
- Defines scaled dot-product attention and multi-head attention as reusable query-key-value interfaces for self-attention and cross-attention.
- Combines global attention with positional encodings, position-wise feed-forward networks, residual connections, and post-layer normalization in a complete trainable architecture.
- Shows how causal masking preserves autoregressive target prediction while allowing all known target positions to be processed in parallel during training.
- Provides end-to-end translation comparisons and targeted design ablations that connect the architectural proposal to empirical quality and compute.

## Limitations

- Full self-attention uses $O(n^2)$ pairwise interactions and storage with sequence length; the proceedings paper proposes restricted attention for long inputs but does not implement or evaluate it.
- The decoder remains autoregressive at inference, so removing recurrent layers does not remove the sequential dependency between generated tokens.
- End-to-end evidence is limited to two WMT 2014 translation directions in the inspected proceedings version; generality beyond that setting is proposed, not established by those experiments.
- Table 3 reports single development-set results without uncertainty or repeated seeds, and several variants change coupled dimensions or total parameter counts.
- The training-cost comparison uses estimated sustained FLOPs across different hardware reported by prior work, so it is an approximate normalization rather than a controlled same-system benchmark.
- The paper visualizes specialized attention patterns, but such examples do not establish that attention weights are faithful causal explanations of model predictions.
- The title can be misleading if read literally: the effective model also relies on feed-forward layers, residual paths, normalization, positional information, embeddings, and a tuned optimization and regularization recipe.

## Key Takeaways

- Scaled dot-product attention is a retrieval interface: queries score keys, the scale controls softmax magnitude, and the resulting weights mix values.
- Multi-head attention preserves several learned retrieval subspaces at roughly the cost of one full-width head in the paper's configuration.
- The same attention primitive serves three distinct interfaces: encoder self-attention, causally masked decoder self-attention, and decoder-to-encoder cross-attention.
- Positional encoding supplies order; feed-forward layers transform features; residual/post-normalization blocks carry and stabilize the representation.
- The paper's architecture parallelizes position processing during training, while causal decoding remains token-by-token at inference.
- Read the 28.4 English-German result as outperforming the listed ensembles and the 41.0 English-French result as outperforming listed single models, not the 41.29 ConvS2S ensemble.

## Relation: Previous Work

> **User-directed revision:** Clarified the historical relationship between recurrent encoder-decoder attention, self-attention, and cross-attention.

- Title: Neural Machine Translation by Jointly Learning to Align and Translate.
  Authors: Dzmitry Bahdanau; Kyunghyun Cho; Yoshua Bengio.
  URL: https://arxiv.org/abs/1409.0473
  - Type: uses
  - Status: not-ingested
  - Work summary: Introduces an encoder-decoder neural translation model that learns soft alignment through additive attention over recurrent encoder states.
  - Role: Supplies the attention-based source retrieval pattern and a central recurrent translation baseline.
  - Limitation: Attention is attached to recurrent encoder-decoder computation, so sequence-aligned recurrence remains.
  - This paper: Retains encoder-decoder attention as cross-attention, adds self-attention within both the encoder and decoder, and removes sequence-aligned recurrence from the architecture.
- Title: Layer Normalization.
  Authors: Jimmy Lei Ba; Jamie Ryan Kiros; Geoffrey E. Hinton.
  URL: https://arxiv.org/abs/1607.06450
  - Type: uses
  - Status: not-ingested
  - Work summary: Normalizes neural activations across a layer's features to stabilize optimization independently of batch statistics.
  - Role: Provides the normalization operation used after every residual sub-layer addition.
  - Limitation: It is an optimization primitive rather than a mechanism for sequence interaction.
  - This paper: Places layer normalization in the post-residual $\operatorname{LayerNorm}(x+\operatorname{Sublayer}(x))$ wrapper around attention and feed-forward sub-layers.
- Title: Deep Residual Learning for Image Recognition.
  Authors: Kaiming He; Xiangyu Zhang; Shaoqing Ren; Jian Sun.
  URL: https://openaccess.thecvf.com/content_cvpr_2016/html/He_Deep_Residual_Learning_CVPR_2016_paper.html
  - Type: uses
  - Status: not-ingested
  - Work summary: Introduces residual mappings that add a block's input to its transformed output, enabling optimization of deeper networks.
  - Role: Supplies the skip-connection pattern around every Transformer sub-layer.
  - Limitation: The original work does not define attention-based sequence processing.
  - This paper: Combines residual addition with layer normalization around each attention and feed-forward transformation.

## Relation: Compared With

- Title: Convolutional Sequence to Sequence Learning.
  Authors: Jonas Gehring; Michael Auli; David Grangier; Denis Yarats; Yann N. Dauphin.
  URL: https://arxiv.org/abs/1705.03122
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Uses stacked convolutional encoders and decoders with attention to parallelize sequence representation during training.
  - Similarity: Both replace recurrent hidden-state traversal with parallel sequence operations and evaluate WMT machine translation.
  - Difference: ConvS2S mixes nearby positions with finite convolution kernels, whereas the Transformer uses global self-attention with a constant theoretical path length between positions.
  - When to use which: The comparison motivates self-attention when global content-dependent interaction and short dependency paths matter; convolution retains a locality bias and linear scaling in sequence length for fixed kernels.
- Title: Neural Machine Translation in Linear Time.
  Authors: Nal Kalchbrenner; Lasse Espeholt; Karen Simonyan; Aaron van den Oord; Alex Graves; Koray Kavukcuoglu.
  URL: https://arxiv.org/abs/1610.10099
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Introduces ByteNet, a convolutional encoder-decoder designed for parallel neural translation with dilated convolutions.
  - Similarity: Both seek to remove recurrent training bottlenecks while retaining a sequence-transduction encoder-decoder structure.
  - Difference: ByteNet relates distant positions through stacked dilated convolutions; the Transformer directly computes content-dependent global interactions in each full self-attention layer.
  - When to use which: The original comparison favors Transformer attention for global dependency paths and reported WMT quality, while convolution offers a structurally local operation whose cost does not form a full $n\times n$ attention matrix.

## Source Notes

- Metadata sources: official NIPS proceedings record and metadata, official proceedings PDF, DBLP record `conf/nips/VaswaniSPUJGKP17`, and arXiv record `1706.03762`.
- Source verification: The complete official 11-page proceedings artifact was acquired and inspected; the title page, architecture diagram, equations, training setup, evidence tables, conclusion, and references match the paper identity.
- Version boundary: The proceedings PDF is the analytical source for this note. Its 28.4 English-German and 41.0 English-French BLEU values override the inconsistent 27.5/41.1 numbers in the official landing-page abstract and result wording in later arXiv revisions.
- Identifier note: The conference record has no verified venue DOI. The arXiv identifier is retained separately rather than treating its later DataCite DOI as a conference DOI.
- PDF policy: No PDF or local filesystem path is stored in the repository note.

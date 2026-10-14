---
citekey: wang2025jenga
title: "JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity"
authors:
  - Tuowei Wang
  - Xingyu Chen
  - Kun Li
  - Ting Cao
  - Ju Ren
  - Yaoxue Zhang
year: 2025
venue: "USENIX ATC 2025"
type: paper
status: ingested
tags:
  - llm
  - ai-infrastructure
  - systems
  - cloud-computing
  - long-context
  - fine-tuning
  - gpu-memory
topics:
  - Long-Context LLMs
  - Activation Memory
  - Token Sparsity
  - Parameter-Efficient Fine-Tuning
areas:
  - AI Infrastructure
  - Systems
  - Cloud Computing
scenarios:
  - Long-Context Fine-Tuning
  - GPU Memory Optimization
  - LLM Training Systems
methods:
  - Contextual Token Sparsity
  - Token Elimination
  - Pattern Prediction
  - Kernel Optimization
doi: null
arxiv: "2501.09767"
official_url: "https://www.usenix.org/conference/atc25/presentation/wang-tuowei"
pdf_url: "https://www.usenix.org/system/files/atc25-wang-tuowei.pdf"
code_url: "https://github.com/Pairshoe/Jenga-AE"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "Title, authors, venue, pages, official URL, PDF URL, and code URL were verified against the local USENIX PDF and the official USENIX paper page."
  - "The arXiv record 2501.09767 appears to be the preprint version under the earlier title 'LeMo: Enabling LEss Token Involvement for MOre Context Fine-tuning'; it has matching authors and technical content."
  - "A secondary ACM DL-style search result listed 10.5555/3768039.3768047, but USENIX and DBLP metadata did not expose a DOI, so doi is kept null."
created: 2026-05-30
updated: 2026-08-09
---

# JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity

## Summary

JENGA is an end-to-end system for reducing memory and time cost in long-context LLM fine-tuning. Its central idea is contextual token sparsity: in long sequences, different token blocks matter in different inputs and layers, so fine-tuning can keep only informative token blocks while preserving the original tensor shape through system-level kernel support. Across OPT and Llama-family evaluations, the paper reports up to 1.93x memory reduction and up to 1.36x speedup while maintaining accuracy close to LoRA.

## Problem

Long-context applications require LLMs to handle sequences beyond their pretrained context windows, and a common way to extend those windows is fine-tuning on longer sequences. In that setting, parameter-efficient fine-tuning methods such as LoRA reduce optimizer and parameter-update overhead but leave activation memory largely untouched. Existing sparse-attention or hidden-dimension sparsity methods can reduce computation, yet they still keep every token involved in the forward/backward path; JENGA calls this shadowy activation, where any participating token casts an activation-memory cost even if only part of its computation is sparse.

## Research Area And Scenario

> **User-directed revision:** Reclassified LLM as the workload/domain rather than a research area; retained AI Infrastructure, Systems, and Cloud Computing as the primary areas.

- Area(s): AI Infrastructure, Systems, and Cloud Computing.
- Scenario(s): Long-context LLM fine-tuning, especially training runs that extend pretrained models to longer context windows and become limited by activation memory and training throughput.
- Why it matters here: JENGA sits at the intersection of model adaptation and systems optimization: it is not a new LLM architecture, but a training-system technique that makes long-context fine-tuning more memory- and time-efficient.
- Indexing rationale: It belongs in AI Infrastructure and Systems because the contribution depends on predictors, token elimination, and custom kernels, and in Cloud Computing through GPU resource efficiency for large training jobs. Long-context LLM adaptation remains the workload context through LLM-specific tags, topics, and scenarios.

## Assumptions And Scope

- Stated assumptions:
  - Claim: The target workload is long-context fine-tuning rather than inference serving.
    - Key constraint: **fine-tuning**.
    - Why it matters: JENGA optimizes activations, gradients, and loss-gradient peaks in the training pipeline rather than KV-cache reuse or request batching.
    - Excludes or weakens: Pure inference-serving systems, retrieval-only long-context pipelines, or workloads where the model is never adapted on long sequences.
  - Claim: Long-context fine-tuning is dominated by activation memory.
    - Key constraint: **activation memory**, especially intermediate results and gradients that grow with sequence length.
    - Why it matters: PEFT can freeze most model weights and still hit memory pressure because activations remain proportional to the long sequence.
    - Excludes or weakens: Short-context fine-tuning, small models, or regimes dominated by optimizer state rather than sequence activations.
  - Claim: The workload contains enough token-level redundancy to eliminate some token blocks safely.
    - Key constraint: **token-level redundancy**.
    - Why it matters: The method depends on the observation that many token blocks in a long input are uninformative for a given layer/input pair.
    - Excludes or weakens: Inputs where nearly every token block is semantically or syntactically essential for the loss, which would reduce safe elimination opportunities.
  - Claim: Informative token blocks can be identified without permanently changing the model output interface.
    - Key constraint: **per-layer token-block masks**.
    - Why it matters: JENGA must eliminate computation for less informative blocks while preserving the surrounding residual shape and training semantics.
    - Excludes or weakens: Architectures or training code that cannot tolerate dynamic per-layer token masks or custom attention/MLP execution paths.
- Implied assumptions:
  - Claim: Predictor training data is representative enough for later fine-tuning runs.
    - Key constraint: **representative sparsity patterns** across model, dataset, and sequence length.
    - Why it matters: The online system relies on lightweight predictors instead of computing full attention scores at runtime.
    - Excludes or weakens: Strong domain shift where token importance patterns differ sharply from predictor training data.
  - Claim: The deployment stack can use custom GPU kernels.
    - Key constraint: **CUDA-style kernel integration** with attention and loss-gradient computation.
    - Why it matters: Large parts of the practical speedup come from avoiding global memory movement and reducing loss-gradient peaks.
    - Excludes or weakens: Hardware/runtime stacks without equivalent kernel customization, or managed training frameworks that hide attention and loss kernels.
- What is ignored or abstracted away:
  - Claim: End-to-end data pipeline, fault tolerance, and multi-tenant scheduling are not the focus.
    - Key constraint: **single training-job efficiency**.
    - Why it matters: The paper measures per-job memory and execution time, not cluster-level admission control, fairness, or cost accounting.
    - Excludes or weakens: Cloud platforms where bottlenecks are queueing, storage bandwidth, or distributed scheduling rather than GPU memory.
- Applies when:
  - Claim: The workload is long-context fine-tuning of transformer LLMs on GPU memory-constrained platforms.
    - Key constraint: **long sequences with redundant token blocks**.
    - Why it matters: The more redundant blocks exist, the more JENGA can cut activation memory and computation.
- May not apply when:
  - Claim: The task demands dense use of nearly all tokens at nearly all layers.
    - Key constraint: **low token sparsity**.
    - Why it matters: Eliminating fewer tokens shrinks the gap over LoRA or LongLoRA and raises the risk of accuracy loss if thresholds are too aggressive.

## Core Idea

JENGA shifts the sparsity unit from hidden dimensions or attention entries to token blocks. Instead of asking whether each token attends densely or sparsely, it asks whether a token block should participate in a layer at all. It then predicts those layer/input-specific token-block masks cheaply and implements them with kernels that avoid materializing repeated token permutations.

## Method

JENGA works as a training-system pipeline around long-context fine-tuning. It first decides which token blocks are informative for each input and layer, then avoids spending attention, MLP, and loss-gradient memory on less useful blocks, and finally uses custom kernels to make that sparsity translate into real memory and runtime savings. The important design point is that token elimination is not treated as an isolated algorithmic trick: it is paired with prediction and kernel execution so the system does not pay more overhead to discover and move sparse tokens than it saves.

The components cooperate in sequence. Information-driven token elimination defines the token-block sparsity signal and layer-specific thresholds; context-aware pattern prediction estimates those sparse patterns cheaply during online fine-tuning; permutation-free token movement keeps the original tensor layout so residual connections and model interfaces remain stable; segment-based peak cutting handles the final loss-gradient memory spike. Extensions such as two-dimensional sparsity and sparsity-aware offloading reuse the same per-layer sparsity information to combine JENGA with other memory optimizations.

- Component: Information-driven token elimination
  - Role: Identify which token blocks are worth keeping in attention and MLP blocks.
  - Input: Token embeddings, attention-score-derived interactions, MLP intermediate activations, model layer identity, and a candidate block size.
  - Output: A retain/eliminate decision for each token block in each layer.
  - Transformation: JENGA aggregates positive attention scores across heads, partitions scores into blocks, takes a block informativeness score, and compares aggregated block scores against a layer-specific threshold. For MLP blocks, it uses intermediate activations as the informativeness signal.
  - Minimal example: Given a 16K-token sequence split into 64-token blocks, a later layer may find most blocks below its threshold and skip their attention/MLP computation, while keeping blocks that contain high-interaction tokens.
- Component: Layer-specific threshold optimization
  - Role: Avoid using one global sparsity threshold for layers whose token-importance distributions differ.
  - Input: Per-layer token-block informativeness profiles and accuracy feedback under small threshold changes.
  - Output: A threshold value for each layer.
  - Transformation: The algorithm initializes thresholds from average block scores and adjusts them using finite-difference accuracy feedback.
  - Minimal example: If one layer has sparse important blocks and another layer has broader token use, the first can receive a more aggressive threshold while the second remains conservative.
- Component: Context-aware pattern prediction
  - Role: Predict sparsity patterns without computing and storing full attention scores during online fine-tuning.
  - Input: Contextual token embeddings organized into token blocks.
  - Output: Approximate query/key informativeness scores and an approximate attention-block informativeness matrix.
  - Transformation: Each layer uses a pair of lightweight low-rank neural predictors for Q and K. Their outputs are multiplied to approximate attention-block informativeness, and elastic size transformation prunes inactive predictor neurons.
  - Minimal example: A predictor sees representative embeddings from each block and outputs scores that mark which blocks should be retained before the expensive attention computation runs.
- Component: Permutation-free token movement
  - Role: Remove the data-movement overhead introduced by dynamic token selection and padding.
  - Input: Original hidden-state tensor, selected token-block mask, and attention outputs for retained blocks.
  - Output: An updated hidden-state tensor with the original shape.
  - Transformation: Instead of materializing compacted token tensors and then padding them back, JENGA selectively loads retained tokens and performs in-place residual addition into the original layout.
  - Minimal example: If blocks 2 and 5 are retained, the kernel loads those blocks directly, computes attention, and writes their updates back to their original positions while untouched blocks keep their residual path.
- Component: Segment-based peak cutting
  - Role: Reduce the temporary activation peak during final loss-gradient computation.
  - Input: Long sequence logits/loss computation over a large vocabulary.
  - Output: Aggregated gradients equivalent to processing the sequence, with lower peak memory.
  - Transformation: JENGA partitions the sequence into smaller segments, computes each segment's loss and gradient, then discards that segment's activations before moving on.
  - Minimal example: Splitting a long sequence into N loss segments turns one large loss-gradient memory spike into N smaller spikes and reduces the peak roughly with the segmenting factor described in the paper.
- Extensions: The paper also combines token-level sparsity with hidden-dimension sparsity as two-dimensional sparsity, and adapts activation offload volume to the layer-specific sparsity ratio.

## Experiments And Evidence

- Hardware: Experiments use 1 x A800 80GB, 1 x A40 48GB, and 4 x RTX 4090 24GB platforms.
- Models: OPT 350M/1.3B/2.7B/6.7B, Llama2-7B, and Llama3-8B, with evaluated sequence lengths up to 64K depending on model family.
- Workloads and datasets: RedPajama is used for long-context fine-tuning, PG19 and cleaned Arxiv Math Proof-Pile for perplexity, and LongBench after LongAlign-10k instruction tuning for long-text task accuracy.
- Baselines: LoRA represents parameter-efficient fine-tuning; LongLoRA represents sparsity-based long-context fine-tuning.
- Memory results: JENGA reports average memory savings of 38.2% and 50.5% over LoRA at 4K and 8K sequence lengths across six models, and up to 1.93x memory reduction in end-to-end fine-tuning.
- Runtime results: JENGA reports average speedups over LoRA of 10.8% on A800 and 8.6% on A40 at 4K sequence length, with up to 1.36x speedup at longer sequence lengths with recomputation.
- Accuracy results: On PG19, Proof-Pile, and LongBench, the reported accuracy/perplexity changes remain close to LoRA, though some individual LongBench task scores move up or down.
- Ablation evidence: Token elimination saves activation memory in attention and MLP blocks; predictors reach 95.13% average recall; elastic size transformation cuts predictor size by about 64.6%; permutation-free kernels produce large microbenchmark speedups; segment-based peak cutting adds about 15% memory savings in the evaluated setting.

## Contributions

- Identifies contextual token sparsity as a token-block-level sparsity mechanism for long-context LLM fine-tuning.
- Designs token elimination, pattern prediction, and kernel optimization techniques that jointly reduce activation memory and computation.
- Implements JENGA as an end-to-end system compatible with multiple LLM architectures and complementary optimizations such as offloading and hidden-dimension sparsity.
- Evaluates JENGA across multiple model families and GPU platforms, showing memory reduction and speedup while largely preserving model quality.

## Limitations

- JENGA depends on representative sparsity-pattern prediction; strong dataset or model shifts may require retraining or retuning predictors.
- Benefits depend on token-block redundancy. Dense reasoning, code, or retrieval tasks where every block remains important may reduce savings or require conservative thresholds.
- The implementation relies on custom Python/C++ and GPU-kernel work, so portability to non-CUDA accelerators or higher-level managed training frameworks is not automatic.
- Evaluation focuses on OPT and Llama-family models and three GPU platforms; broader architectures, production distributed stacks, and non-English/domain-specialized fine-tuning are less explored.
- Some reported comparisons exclude activation recomputation and offloading unless explicitly stated, so gains may change when combined with a production training stack's existing memory optimizations.

## Key Takeaways

- For long-context fine-tuning, activation memory can remain the dominant bottleneck even after adopting PEFT.
- Sparse attention does not automatically reduce activation memory if all tokens still participate; the token participation boundary matters.
- Predicting token-block importance is the key systems trick: it avoids paying the full attention-score cost just to decide what to skip.
- Kernel design is not incidental here. Without permutation-free movement and peak cutting, algorithmic sparsity can be eaten by data movement and temporary activations.
- JENGA is most compelling as a training-system component for memory-constrained long-context adaptation, not as a general replacement for every long-context LLM optimization.

## Relation: Previous Work

- Title: Long Exposure: Accelerating Parameter-Efficient Fine-Tuning for LLMs under Shadowy Sparsity
  Authors: Tuowei Wang; Kun Li; Zixu Hao; Donglin Bai; Ju Ren; Yaoxue Zhang; Ting Cao; Mao Yang.
  URL: https://www.likun.tech/pdf/sc24_longexposure.pdf
  - Type: improves
  - Status: not-ingested
  - Work summary: Long Exposure studies shadowy sparsity in parameter-efficient LLM fine-tuning and builds a system that exposes, predicts, and executes sparse computation to speed PEFT.
  - Role: Prior sparsity-oriented PEFT work that motivates shadowy sparsity and hidden-dimension-level optimization.
  - Limitation: It does not remove whole token blocks from participation, so activation memory can still scale with the full token sequence.
  - This paper: Moves the sparsity boundary to token blocks and adds predictors plus kernels to make that boundary practical during fine-tuning.
- Title: FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning
  Authors: Tri Dao.
  URL: https://arxiv.org/abs/2307.08691
  - Type: uses
  - Status: not-ingested
  - Work summary: FlashAttention-2 improves exact attention performance by redesigning work partitioning and parallelism in the attention kernel.
  - Role: Provides the optimized attention-kernel context into which JENGA integrates predictor training and attention execution.
  - Limitation: FlashAttention optimizes exact attention IO/computation but does not decide which long-context token blocks can be skipped during fine-tuning.
  - This paper: Adds contextual token sparsity and kernel fusion around attention to reduce token participation and global memory movement.
- Title: LongBench: A Bilingual, Multi-Task Benchmark for Long Context Understanding
  Authors: Yushi Bai; Xin Lv; Jiajie Zhang; Hongchang Lyu; Jiankai Tang; Zhidian Huang; Zhengxiao Du; Xiao Liu; Aohan Zeng; Lei Hou; et al.
  URL: https://arxiv.org/abs/2308.14508
  - Type: benchmark
  - Status: not-ingested
  - Work summary: LongBench evaluates long-context understanding across bilingual, multi-task long-text settings.
  - Role: Benchmark used to test long-text task quality after applying JENGA.
  - Limitation: LongBench measures task quality, not training-system memory behavior.
  - This paper: Uses it to check that token-block elimination does not materially damage downstream long-context capability.

## Relation: Compared With

- Title: LoRA: Low-Rank Adaptation of Large Language Models
  Authors: Edward J. Hu; Yelong Shen; Phillip Wallis; Zeyuan Allen-Zhu; Yuanzhi Li; Shean Wang; Lu Wang; Weizhu Chen.
  URL: https://arxiv.org/abs/2106.09685
  - Type: compares-with
  - Status: not-ingested
  - Work summary: LoRA freezes pretrained weights and injects trainable low-rank adapters, reducing trainable parameters and optimizer-state overhead during fine-tuning.
  - Similarity: Both target efficient LLM fine-tuning and can be used together because JENGA preserves the broader fine-tuning dynamics.
  - Difference: LoRA reduces trainable parameter and optimizer-state costs; JENGA targets activation memory and token participation during long-context fine-tuning.
  - When to use which: Use LoRA as the PEFT method; add JENGA when long sequence activations dominate memory or runtime.
- Title: LongLoRA: Efficient Fine-tuning of Long-Context Large Language Models
  Authors: Yukang Chen; Shengju Qian; Haotian Tang; Xin Lai; Zhijian Liu; Song Han; Jiaya Jia.
  URL: https://arxiv.org/abs/2309.12307
  - Type: compares-with
  - Status: not-ingested
  - Work summary: LongLoRA extends LoRA for long-context fine-tuning by using shifted sparse local attention to reduce attention computation while scaling context length.
  - Similarity: Both address efficient fine-tuning for long-context LLMs and evaluate long sequence scaling.
  - Difference: LongLoRA uses shifted sparse local attention and hidden-dimension-style sparsity; JENGA eliminates less informative token blocks and directly reduces activation memory.
  - When to use which: Use LongLoRA-style methods when attention-computation reduction is the goal; use JENGA when activation memory is the binding constraint.

## Source Notes

- Metadata sources:
  - Local PDF provided by the user, inspected without recording the local path.
  - Official USENIX page: https://www.usenix.org/conference/atc25/presentation/wang-tuowei
  - Official USENIX PDF: https://www.usenix.org/system/files/atc25-wang-tuowei.pdf
  - Code repository listed in the paper: https://github.com/Pairshoe/Jenga-AE
  - arXiv preprint record: https://arxiv.org/abs/2501.09767
- Source verification: The local PDF text was inspected directly, including abstract, method, evaluation, related work, references, and artifact appendix. Bibliographic fields were cross-checked against official USENIX metadata and secondary bibliographic metadata.
- PDF policy: The PDF was not copied into the vault or repository. The paper note records the official public PDF URL, not the user's local filesystem path.

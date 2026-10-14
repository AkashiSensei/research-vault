---
citekey: kwon2023pagedattention
title: "Efficient Memory Management for Large Language Model Serving with PagedAttention"
authors:
  - Woosuk Kwon
  - Zhuohan Li
  - Siyuan Zhuang
  - Ying Sheng
  - Lianmin Zheng
  - Cody Hao Yu
  - Joseph E. Gonzalez
  - Hao Zhang
  - Ion Stoica
year: 2023
venue: "SOSP 2023"
type: paper
status: ingested
tags:
  - llm
  - ai-infrastructure
  - systems
  - inference-serving
  - kv-cache
  - memory-management
  - paged-attention
topics:
  - LLM Serving Systems
  - KV Cache Management
  - Continuous Batching
areas:
  - AI Infrastructure
  - Systems
scenarios:
  - LLM Inference Serving
  - GPU Memory Optimization
methods:
  - PagedAttention
  - Block-Based KV Cache Management
  - Preemptive Scheduling
  - Copy-on-Write
doi: "10.1145/3600006.3613165"
arxiv: "2309.06180"
official_url: "https://dl.acm.org/doi/10.1145/3600006.3613165"
pdf_url: "https://arxiv.org/pdf/2309.06180"
code_url: "https://github.com/vllm-project/vllm"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The title, authors, SOSP 2023 venue, DOI, and paper identity were verified against the ACM record, the DBLP record, and the first page of the legal arXiv full text."
  - "The complete 16-page paper was inspected, including the memory analysis, system design, evaluation, ablations, discussion, related work, and references."
  - "The arXiv PDF was used as the accessible legal full-text copy because direct automated retrieval of the ACM PDF was unavailable; its first page carries the SOSP 2023 citation and matching DOI."
created: 2026-08-07
updated: 2026-08-14
---

# Efficient Memory Management for Large Language Model Serving with PagedAttention

## Learning Path Position

> **User-directed revision:** Recast the learning path as a three-layer architecture, grouped execution control and resource management as complementary runtime responsibilities, and marked this paper's focus within that layer.

The following is an analytical three-layer architecture for LLM inference
serving, ordered by widening system boundary rather than a strict dependency or
citation chain: one inference engine, one end-to-end service, and then the
cluster platform.

1. **Inference-engine runtime — this paper.** Make one logical serving engine
   execute concurrent requests efficiently through two complementary
   responsibilities:
   - **Request execution control.** Decide how individual requests make
     progress and how runnable requests share each execution opportunity.
   - **Engine-local state and resource management — this paper's focus.**
     Manage the state, memory, and local compute capacity needed to make those
     execution plans feasible.
2. **Service architecture and resource coordination.** Organize major serving
   components or stages and coordinate their resources against end-to-end
   performance objectives.
3. **Cluster platform orchestration.** Coordinate placement, load balancing,
   state movement, elasticity, and policy across serving instances.

Within the first layer, execution control decides who advances and when, while
resource management determines whether and how the required state and capacity
can be supplied. Neither responsibility sits above the other: scheduling
creates resource demand, and available capacity constrains scheduling. This
paper contributes primarily to engine-local state and resource management
through paged KV-cache allocation, sharing, and reclamation.

## Summary

> **User-directed revision:** Made the two cooperating layers behind PagedAttention explicit and clarified that the contribution is more than paging-style memory allocation.

vLLM begins where iteration-level scheduling leaves off. A continuous-batching scheduler can interleave many requests, but it can run only as many as their key/value (KV) caches fit in GPU memory. Existing engines allocate each request a contiguous region sized for a predicted or maximum sequence length. Because outputs grow unpredictably, that policy creates reserved-but-unused capacity, internal fragmentation, external fragmentation, and duplicated state for related sequences.

PagedAttention is not just paging-style KV allocation; vLLM relies on two cooperating layers:

- **Attention-computation layer:** The PagedAttention kernel follows a block table so exact attention can read logical KV blocks from non-contiguous physical GPU memory.
- **KV-cache management layer:** The runtime allocates, maps, shares, and reclaims fixed-size blocks on demand, coordinating them with scheduling and preemption.

Together, the kernel makes non-contiguous state computable, while the runtime turns that capability into usable serving capacity. The paper's throughline is:

1. Better memory utilization admits more concurrent sequences.
2. More admitted sequences produce larger effective batches.
3. Larger batches amortize model-weight movement and increase serving throughput.

Across OPT-13B/66B/175B and LLaMA-13B workloads derived from ShareGPT, Alpaca, and translation data, the paper reports roughly 2–4x throughput over the evaluated FasterTransformer and Orca-style baselines at comparable latency. The contribution is not paging by analogy alone; it is the attention kernel and runtime contract that turns non-contiguous state into usable serving capacity.

## Problem

Autoregressive LLM serving is often memory-bound. Model parameters occupy most GPU memory, while each active sequence maintains a KV cache that grows with every token. For OPT-13B, the paper calculates 800 KB of KV state per token and up to 1.6 GB for a 2,048-token request. The number of requests that can be batched is therefore constrained by how efficiently this dynamic state is stored.

Contiguous maximum-length allocations fit conventional tensor runtimes but mismatch the workload. Output lengths are unknown, allocations expand and disappear at different times, and advanced decoding creates multiple sequences that share part of their history. The paper measures only 20.4%–38.2% of KV-cache memory holding actual token state in its Orca-style baselines under the evaluated workloads. The rest is reservation or fragmentation, while duplicated prompts and beam prefixes create another avoidable cost. The system needs an attention implementation that can consume non-contiguous state and a runtime that can allocate, share, evict, and recover that state safely.

## Research Area And Scenario

> **User-directed revision:** Reclassified LLM as the workload/domain, retained AI Infrastructure and Systems as the primary areas, and clarified the interpretation of parallel sampling and beam search.

- Area(s): AI Infrastructure and Systems.
- Scenario(s): High-throughput online LLM inference, GPU KV-cache management, continuous batching, distributed tensor-parallel inference, parallel sampling, and beam search.
- Decoding-scenario note: Parallel sampling and beam search are real serving scenarios, but the paper does not establish that they dominated production traffic; it emphasizes them mainly because shared prompts and beam prefixes clearly expose PagedAttention's block-sharing capability.
- Why it matters here: PagedAttention connects a measurable memory-allocation failure mode to scheduler admission and end-to-end throughput, providing a durable systems abstraction used by later serving platforms.
- Indexing rationale: It belongs in AI Infrastructure and Systems because the contribution spans kernels, memory management, scheduling, and distributed execution. LLM inference remains the workload context through the LLM Inference Serving and GPU Memory Optimization scenarios and LLM-specific tags and topics.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Online generation uses Transformer attention with a persistent KV cache that grows token by token.
    - Key constraint: **dynamic per-sequence KV state**.
    - Why it matters: On-demand blocks help only when state size and lifetime are not known well enough for efficient static allocation.
    - Excludes or weakens: Stateless inference, one-shot encoders, and workloads whose intermediate state is small or statically shaped.
  - Claim: Serving throughput is constrained by how many sequence working sets fit in GPU memory.
    - Key constraint: **KV-cache capacity is a binding bottleneck**.
    - Why it matters: Memory savings improve throughput only if they admit useful additional batch work.
    - Excludes or weakens: Compute-bound regimes with short sequences and abundant KV capacity; the paper observes this boundary for OPT-175B on the short Alpaca workload.
  - Claim: The inference stack can execute custom kernels over a logical-to-physical block table.
    - Key constraint: **GPU-kernel and runtime integration**.
    - Why it matters: Conventional attention kernels assume contiguous KV tensors; PagedAttention must fetch blocks and fuse the resulting indirection into attention.
    - Excludes or weakens: Black-box model APIs or accelerator stacks without an equivalent non-contiguous attention primitive.
  - Claim: Requests and all related sequences can be scheduled as a group under FCFS.
    - Key constraint: **sequence-group all-or-nothing scheduling**.
    - Why it matters: Beam candidates may share physical blocks, so preemption and recovery must preserve the group together.
    - Excludes or weakens: Policies that independently migrate or prioritize individual beams without maintaining shared-block consistency.

> **User-directed revision:** Clarified the fixed per-token KV footprint assumed within one cache pool, the causal-prefix equivalence required for recomputation, and the logical alignment required to share block IDs across workers.

- Implied assumptions:
  - Claim: Requests sharing one physical KV-block pool use a uniform per-token KV layout.
    - Key constraint: **fixed per-token KV footprint within each pool**.
    - Why it matters: A block defined by a fixed token count has one allocator size only when every token slot has the same byte size.
    - Excludes or weakens: Mixing models or per-request KV layouts, data types, or sharding schemes in one pool without separate arenas or size classes.
  - Claim: For a decoder-only causal model, the KV state of a fixed prefix can be reconstructed by treating the original prompt and generated tokens as one known prefill sequence.
    - Key constraint: **unchanged tokens, positions, masks, and model state**.
    - Why it matters: This makes recomputation a correct recovery path after eviction rather than a replay of token sampling.
    - Excludes or weakens: Architectures or pipelines where the prompt/output boundary changes attention, position, or model-state semantics.
  - Claim: A common fixed block size is a workable compromise across the workload.
    - Key constraint: **block-size tradeoff between kernel parallelism and internal fragmentation**.
    - Why it matters: Small blocks increase indexing and transfer overhead; large blocks waste capacity and reduce sharing. The paper selects 16 tokens as its default for the evaluated workloads.
    - Excludes or weakens: Highly bimodal or extreme sequence distributions that would benefit from adaptive block sizes.
  - Claim: A centralized scheduler can distribute the same block mapping when workers align on token-slot identity, even though equal cross-worker byte strides are not a mathematical requirement of the mapping.
    - Key constraint: **a common block-ID domain with worker-local interpretation**.
    - Why it matters: Each worker can resolve block ID 5 against its own KV-cache shard as long as that ID denotes the same logical token range everywhere.
    - Excludes or weakens: Layouts that cannot preserve aligned token positions or a common valid block-ID range; the paper itself evaluates homogeneous Megatron-style tensor parallelism and does not demonstrate heterogeneous local block sizes.
- What is ignored or abstracted away:
  - Claim: The system manages one model deployment rather than an elastic multi-replica fleet.
    - Key constraint: **within-engine memory and scheduling**.
    - Why it matters: PagedAttention determines which requests fit inside an engine, not where requests should route across replicas or clusters.
    - Excludes or weakens: Cross-instance load balancing, autoscaling, live migration, failure recovery, and cluster-wide placement.
  - Claim: Prefill and decode share the same serving engine and resource plan.
    - Key constraint: **co-located inference phases**.
    - Why it matters: The paper does not optimize time-to-first-token versus inter-token latency through phase disaggregation or phase-specific SLO planning.
    - Excludes or weakens: Workloads dominated by prefill/decode interference or deployments requiring independent phase scaling.
- Applies when:
  - Claim: Sequence lengths are variable, KV state is large, and enough demand exists to exploit additional memory capacity as larger batches.
    - Key constraint: **memory-bound continuous batching under sustained load**.
    - Why it matters: This is where eliminating reservation, fragmentation, and duplicated prefixes directly increases throughput.
- May not apply when:
  - Claim: Attention state is small, serving is compute-bound, or the kernel indirection cost outweighs admitted concurrency.
    - Key constraint: **no useful batch-size increase**.
    - Why it matters: PagedAttention's custom kernel has measurable overhead; without a memory-capacity benefit, a contiguous implementation may be faster.

## Core Idea

Separate the logical sequence layout from physical KV-cache placement. Logical blocks remain ordered by token position, while each block can map to any free fixed-size physical block. PagedAttention follows the block table during attention, so the runtime can allocate state only when needed, eliminate external fragmentation, cap per-sequence internal waste at one block, and share immutable history through reference counts and copy-on-write.

## Method

> **User-directed revision:** Added a compact system-level map and clarified global softmax and physical-versus-logical ordering in PagedAttention.

At a glance, vLLM has three cooperating design components:

- **Centralized Scheduler:** Decides which requests advance in each iteration.
- **KV Cache Manager:** Allocates, maps, shares, and reclaims KV blocks.
- **GPU Workers + PagedAttention:** Execute the model according to each sequence's block table.

vLLM centers on a centralized scheduler and KV-cache manager coordinating one or more GPU workers. Before each generation iteration, the scheduler selects request sequence groups, allocates any newly needed physical blocks, and sends token IDs plus block tables to the workers. Workers execute the model; the PagedAttention kernel reads each sequence's scattered KV blocks, and fused kernels append new state. Sampled tokens return to the scheduler, which updates sequence state and repeats.

The same block abstraction supports richer decoding. Parallel samples initially map their logical prompt blocks to the same physical blocks. Beam candidates share every common prefix block. Reference counts protect shared blocks, and a write to a shared final block triggers copy-on-write. When demand exceeds GPU capacity, vLLM preempts newer sequence groups and recovers them through recomputation or CPU swapping.

- Component: PagedAttention kernel
  - Role: Compute exact attention while KV keys and values reside in non-contiguous physical blocks.
  - Input: A query vector, an ordered block table for the sequence, filled-position metadata, and the physical key/value blocks.
  - Output: The same attention output that a logically contiguous KV cache would produce.
  - Transformation: The kernel fetches mapped blocks and combines their partial scores and value products under one globally normalized softmax over all eligible logical token positions; blocks are not normalized independently. Physical blocks may be visited or reduced in any order, while logical positions remain available for correct K/V pairing, positional semantics, and masking. Kernel fusion reduces the cost of the extra block-table lookup and branching.
  - Minimal example: A sequence's first three logical KV blocks can map to physical blocks 7, 1, and 3; attention follows that mapping without copying them into one contiguous allocation.
- Component: Logical/physical KV block manager
  - Role: Allocate growing sequence state on demand and reclaim it promptly.
  - Input: Per-sequence logical length, fixed block size, free physical blocks, and completion events.
  - Output: Block-table mappings, reference counts, filled-position metadata, and released capacity.
  - Transformation: Logical blocks fill from left to right; a new physical block is allocated only when the previous block is full, and all blocks are freed when the sequence completes.
  - Minimal example: With four-token blocks, a seven-token prompt needs two physical blocks rather than a maximum-length region; the eighth token fills the second block, and only the ninth requires a third.
- Component: Block-level sharing and copy-on-write
  - Role: Avoid duplicating KV state shared by parallel samples, beam candidates, or predefined prefixes.
  - Input: Multiple logical sequences with a shared token prefix and reference counts for their mapped physical blocks.
  - Output: Shared immutable blocks plus private blocks only where sequences diverge.
  - Transformation: Forked sequences initially reference the same physical blocks. A write to a multiply referenced partial block allocates one new block, copies that block, and decrements the old reference count.
  - Minimal example: Two samples share all prompt blocks; when their first generated tokens differ, only the last partially filled block is copied instead of the entire prompt cache.
- Component: FCFS scheduler and preemption
  - Role: Select runnable sequence groups when request demand or KV growth exceeds available blocks.
  - Input: Arrival order, active and waiting sequence groups, predicted block demand for the next iteration, and GPU/CPU block availability.
  - Output: A runnable batch and, when necessary, a set of newer groups to preempt.
  - Transformation: Earlier arrivals are retained first, all sequences belonging to one request are gang-scheduled, and evicted groups recover through prompt recomputation or GPU-to-CPU swapping.
  - Minimal example: If an active generation needs another block and none is free, the newest sequence group can be evicted, its blocks recovered later, and the older request can continue without starvation.
- Component: Distributed tensor-parallel executor
  - Role: Extend block-managed serving to models larger than one GPU.
  - Input: Centralized token IDs and block tables, tensor-parallel model shards, and per-worker KV-cache shards.
  - Output: Synchronized model activations, sampled tokens, and updated KV state.
  - Transformation: Every worker receives the same logical mapping but stores only its attention-head shard; NCCL all-reduce synchronizes model computations while memory-management decisions remain centralized.
  - Minimal example: Four workers serving OPT-66B interpret physical block ID 5 consistently, even though each stores only the keys and values for its assigned heads.

> **User-directed revision:** Added a clearly separated post-publication implementation note showing how later vLLM versions realize worker-local block layouts without attributing that capability to the SOSP 2023 evaluation.

- Post-publication implementation extension: Later vLLM pipeline-parallel cache configuration projects the global KV-cache plan onto each worker's local layer set and available memory, computes worker-local capacities, and restricts the shared block-ID range to the smallest capacity across workers. A common block ID can therefore carry different aggregate local byte costs while preserving the same token-slot meaning. This remains a controlled form of heterogeneity rather than arbitrary layouts: the current implementation still requires the KV-cache specification for the same layer to match across workers. See the [current vLLM KV-cache configuration source](https://docs.vllm.ai/en/latest/api/vllm/v1/core/kv_cache_utils/).

## Experiments And Evidence

- **vLLM vs. Orca-style contiguous allocation → paged KV management increases the sustainable request rate.**
  - Selected evidence: On ShareGPT-derived traces, vLLM sustains 1.7x–2.7x higher request rates than the infeasible Orca (Oracle) variant and 2.7x–8x higher rates than Orca (Max) at similar normalized latency. The abstract summarizes overall gains over evaluated state-of-the-art systems as 2–4x.
  - Supports: Even with iteration-level scheduling, memory reservation and fragmentation can be the dominant limit on batch concurrency.
  - Original: Sections 6.1–6.2 and Figure 12.
- **Actual batch occupancy → memory efficiency is the mechanism connecting paging to throughput.**
  - Selected evidence: For OPT-13B at the specified traces, vLLM averages 30.42 batched requests versus 13.62 for Orca (Oracle) on ShareGPT, and 132.44 versus 72.75 on Alpaca; Orca (Max) averages 7 in both examples.
  - Supports: vLLM's throughput gain is associated with fitting more live request state, not merely a faster attention kernel.
  - Original: Figure 13.
- **Parallel sampling and beam search → block sharing compounds the fragmentation benefit.**
  - Selected evidence: On Alpaca, sharing saves 6.1%–9.8% of KV blocks for parallel sampling and 37.6%–55.2% for beam search; the paper reports even larger ranges on ShareGPT. At beam width 6, vLLM's throughput advantage over Orca (Oracle) rises from 1.3x for basic sampling to 2.3x.
  - Supports: Reference counting and copy-on-write exploit decoding structure that independent contiguous allocations cannot express.
  - Original: Section 6.3 and Figures 14–15.
- **Shared prefixes → the block abstraction can reuse prompt state across independent requests.**
  - Selected evidence: On the LLaMA-13B translation workload, vLLM reports 1.67x higher throughput than Orca (Oracle) with an 80-token one-shot shared prefix and 3.58x with a 341-token five-shot prefix.
  - Supports: The same logical/physical mapping provides a practical mechanism for cross-request prefix-cache reuse.
  - Original: Section 6.4 and Figure 16.
- **PagedAttention kernel vs. contiguous FasterTransformer kernel → paging has a local cost.**
  - Selected evidence: The custom PagedAttention kernel has 20%–26% higher attention-kernel latency in the reported microbenchmark because of block-table access, extra branches, and variable-length handling.
  - Supports: The end-to-end result comes from greater concurrency; PagedAttention is not intrinsically a faster attention kernel.
  - Original: Section 7.1 and Figure 18a.
- **Block size and recovery policy → configuration affects both fragmentation and overhead.**
  - Selected evidence: A 16-token block works well across the two tested traces and becomes the default. Recomputation and swapping have comparable end-to-end performance for block sizes 16–64; swapping is inefficient for very small blocks because many small PCIe transfers underuse bandwidth.
  - Supports: Paging requires workload-aware systems choices rather than one universally optimal block or eviction policy.
  - Original: Sections 7.2–7.3 and Figures 18b–19.
- **Evaluation boundary.**
  - Selected evidence: The paper evaluates OPT-13B/66B/175B and LLaMA-13B on Google Cloud A100 configurations. ShareGPT and Alpaca provide real input/output length distributions, but timestamps are synthesized with Poisson arrivals; most traces last one hour and OPT-175B traces last 15 minutes.
  - Supports: The experiments are broad for their period but do not directly measure modern long-context, GQA/MQA, heterogeneous-cluster, or production burst behavior.
  - Original: Section 6.1, Table 1, and Figure 11.

## Contributions

- Identifies reservation, internal fragmentation, external fragmentation, and redundant KV-cache duplication as first-order limits on continuous-batching throughput.
- Introduces PagedAttention, an exact attention algorithm that follows non-contiguous fixed-size KV blocks.
- Builds vLLM by co-designing PagedAttention with on-demand block allocation, copy-on-write sharing, preemptive scheduling, and distributed execution.
- Evaluates the system across model scales, workloads, and decoding methods while exposing kernel overheads and memory-policy tradeoffs.

## Limitations

- Request arrival times are generated from Poisson processes because the public text datasets lack timestamps; correlated sessions, bursts, and production queueing behavior are not measured.
- Orca was not publicly available, so the authors reimplemented it and assumed a buddy allocator plus three output-reservation policies. The comparison is informative but not an execution of the original Orca artifact.
- The evaluated OPT/LLaMA models have a 2,048-token-era context and use A100 hardware. Modern GQA/MQA models, much longer contexts, speculative decoding, and newer fused kernels can change KV size and kernel tradeoffs.
- PagedAttention adds 20%–26% latency to the isolated attention kernel in the reported microbenchmark; gains weaken when the workload is compute-bound or saved memory cannot increase useful batch occupancy.
- A fixed block size leaves up to one block of internal waste per sequence and may be suboptimal for unusual length distributions.
- vLLM's paper design focuses on one model engine. It does not solve cross-replica load balancing, live migration, prefill/decode disaggregation, cluster-wide SLO placement, or fault tolerance.

## Key Takeaways

- Continuous batching exposes memory allocation as a scheduling decision: a request cannot join the batch unless its KV working set can be represented efficiently.
- PagedAttention's durable abstraction is logical continuity over physically non-contiguous state, not paging terminology by itself.
- Copy-on-write makes decoding structure actionable; shared prompts and beam prefixes become memory objects rather than duplicated tensors.
- End-to-end speedup comes from fitting more concurrent work despite a slower isolated attention kernel.
- Read Orca first for the iteration-level execution lifecycle, then vLLM for the memory substrate that lets that lifecycle scale.

## User Interpretation: Differences From OS Paging

> **User-directed revision:** Added the user's refined comparison between OS paging and PagedAttention in the original Chinese wording together with an English translation.

1. 首先，OS 的页式内存管理有硬件参与，通过页表基址寄存器、MMU 和 TLB 等机制加速地址映射；而 PagedAttention 的 KV 分页管理主要由软件实现，通过软件完成 block 分配和逻辑—物理映射。关键映射关系最终需要放在 GPU kernel 可以快速获取的存储中。需要注意，这里的“物理 block”是 vLLM 的 KV Cache 内存池中的 slot，底层仍然存在 GPU 自身的虚拟内存和硬件地址翻译。
2. 在采用按需分页的 OS 中，新的虚拟页面通常不会预先获得物理页框，而是在程序第一次访问时触发缺页异常，再由操作系统分配、映射或换入相应页面。PagedAttention 不采用这种“先访问、再通过异常补页”的机制，因为推理系统了解 KV Cache 的访问语义：虽然无法预知请求最终会生成多少 token，但在每轮执行前，可以准确知道本轮需要访问哪些已有 blocks，以及是否需要为新增 token 分配新的 block。因此，KV Cache Manager 会在 kernel 启动前主动完成所需 block 的分配和驻留，无需依赖缺页异常。
3. 换入换出的决策粒度不同。OS 更常利用程序的局部性，以页面为单位换入换出；而 PagedAttention 虽然以 block 为单位分配、共享和回收，但抢占时需要以完整序列为单位，必要时还要以共享状态的 sequence group 为单位，因为缺少任何一部分历史 KV Cache，序列都无法继续计算。另外，KV Cache 可以直接丢弃并根据 token 重新计算，虽然重算并非始终便宜；OS 也可以丢弃能够从文件等后备存储恢复的页面，但不能直接丢弃无法恢复的程序状态。
4. 由于映射关系由软件处理，它在不同 GPU 的本地存储布局上相对灵活。同一个推理引擎中，每个 block 包含的逻辑 token 数量必须固定，运行过程中不能动态变化；多个 GPU 共享逻辑 block 到物理 slot 编号的映射，但每个 slot 在不同 GPU 上实际占用的字节数，可以由该 GPU 保存的 KV shard 决定，并不天然要求完全一致。不过，论文实际实现的是 attention-head 维度的 tensor parallelism；不均匀的按层划分属于这套抽象可以支持的扩展，而不是论文已经验证的实现。

### English Translation

1. First, OS paging involves hardware support, using mechanisms such as the page-table base register, MMU, and TLB to accelerate address translation. PagedAttention's paged KV management, by contrast, is implemented mainly in software, which handles block allocation and logical-to-physical mapping. The key mappings ultimately need to reside in storage that GPU kernels can access efficiently. Here, a “physical block” is a slot in vLLM's KV-cache memory pool; the underlying GPU virtual-memory system and hardware address translation still remain in effect.
2. In a demand-paged OS, a new virtual page is usually not assigned a physical frame in advance. Its first access triggers a page fault, after which the OS allocates, maps, or pages in the corresponding frame. PagedAttention does not follow this “access first, then provision through an exception” model because the inference system understands the access semantics of the KV cache. Although it cannot know how many tokens a request will eventually generate, before each iteration it can determine which existing blocks will be accessed and whether a new block is needed for the next token. The KV Cache Manager therefore allocates and makes the required blocks resident before launching the kernel, without relying on page faults.
3. The decision granularity for paging data in and out is different. An OS commonly exploits program locality and moves data at page granularity. PagedAttention allocates, shares, and reclaims memory at block granularity, but preemption must operate on a complete sequence—and, when state is shared, sometimes on an entire sequence group—because a sequence cannot continue if any part of its historical KV cache is missing. A KV cache may also be discarded and recomputed from tokens, although recomputation is not always cheap. An OS can likewise discard pages recoverable from files or other backing stores, but it cannot simply discard unrecoverable program state.
4. Because the mapping is handled in software, the local storage layout can be relatively flexible across GPUs. Within one inference engine, the number of logical token positions represented by each block must remain fixed and cannot change dynamically during execution. Multiple GPUs share the mapping from logical blocks to physical slot IDs, but the number of bytes occupied by a slot on each GPU may depend on the KV shard stored by that GPU and therefore need not be intrinsically identical. The paper itself implements tensor parallelism along the attention-head dimension; uneven layer-wise partitioning is an extension that this abstraction could support, rather than an implementation validated by the paper.

## Relation: Previous Work

- Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  Authors: Gyeong-In Yu; Joo Seong Jeong; Geon-Woo Kim; Soojeong Kim; Byung-Gon Chun.
  URL: https://www.usenix.org/conference/osdi22/presentation/yu
  Obsidian: [[yu2022orca]]
  - Type: extends
  - Status: ingested
  - Work summary: Orca introduces iteration-level scheduling and selective batching so active autoregressive requests can be regrouped every token step.
  - Role: Supplies vLLM's fine-grained scheduling baseline and the execution pattern whose concurrency is constrained by KV-cache memory.
  - Limitation: Its evaluated allocation policies reserve contiguous output capacity and cannot efficiently share state across related sequences.
  - This paper: Keeps the iteration-level serving model while replacing contiguous reservations with on-demand blocks and block-level sharing.
- Title: FlashAttention: Fast and Memory-Efficient Exact Attention with IO-Awareness
  Authors: Tri Dao; Daniel Y. Fu; Stefano Ermon; Atri Rudra; Christopher Ré.
  URL: https://arxiv.org/abs/2205.14135
  - Type: uses
  - Status: not-ingested
  - Work summary: FlashAttention tiles exact attention to reduce high-bandwidth-memory traffic and peak intermediate memory.
  - Role: Represents kernel-level attention optimization and the broader memory/IO context for vLLM.
  - Limitation: It optimizes computation over a sequence but does not allocate persistent KV state across dynamically arriving serving requests.
  - This paper: Adds a serving-time logical/physical KV memory layer and scheduler integration; it addresses a different memory object and lifecycle.

## Relation: Compared With

- Title: FasterTransformer
  Authors: NVIDIA.
  URL: https://github.com/NVIDIA/FasterTransformer
  - Type: compares-with
  - Status: not-ingested
  - Work summary: FasterTransformer provides highly optimized Transformer inference kernels and distributed execution primitives.
  - Similarity: Both target efficient GPU inference for large Transformer models.
  - Difference: FasterTransformer's evaluated service uses conventional contiguous KV allocation and coarse batching; vLLM adds iteration scheduling, paged state, sharing, and preemption.
  - When to use which: A kernel library is appropriate when operator latency dominates; a vLLM-style engine is appropriate when online request concurrency and KV capacity dominate.
- Title: Orca: A Distributed Serving System for Transformer-Based Generative Models
  Authors: Gyeong-In Yu; Joo Seong Jeong; Geon-Woo Kim; Soojeong Kim; Byung-Gon Chun.
  URL: https://www.usenix.org/conference/osdi22/presentation/yu
  Obsidian: [[yu2022orca]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Orca dynamically interleaves requests at each generation iteration and selectively batches operators with compatible shapes.
  - Similarity: Both use fine-grained autoregressive scheduling to keep the GPU busy under variable request lengths.
  - Difference: Orca's central contribution is scheduling/execution; vLLM's is the memory interface that admits more requests under that schedule.
  - When to use which: Use the two together as complementary layers; Orca explains when work becomes runnable, while vLLM explains how its growing state fits.

## Source Notes

- Metadata sources:
  - Official ACM Digital Library record: https://dl.acm.org/doi/10.1145/3600006.3613165
  - Legal arXiv full text: https://arxiv.org/pdf/2309.06180
  - DBLP bibliographic record: https://dblp.org/rec/conf/sosp/KwonLZ0ZY0ZS23
  - Code repository named in the paper: https://github.com/vllm-project/vllm
- Source verification: The complete legal full text was inspected directly, including the memory analysis, design, implementation, end-to-end experiments, ablations, discussion, related work, and references. The publication identity on the first page and representative evaluation figures were visually checked against the ACM DOI metadata.
- PDF policy: The PDF was not added to the vault or repository. This note records only public source URLs and no local filesystem path.

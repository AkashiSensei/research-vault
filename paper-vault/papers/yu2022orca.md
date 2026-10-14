---
citekey: yu2022orca
title: "Orca: A Distributed Serving System for Transformer-Based Generative Models"
authors:
  - Gyeong-In Yu
  - Joo Seong Jeong
  - Geon-Woo Kim
  - Soojeong Kim
  - Byung-Gon Chun
year: 2022
venue: "OSDI 2022"
type: paper
status: ingested
tags:
  - llm
  - ai-infrastructure
  - systems
  - inference-serving
  - continuous-batching
  - distributed-inference
topics:
  - LLM Serving Systems
  - Continuous Batching
  - Autoregressive Inference
areas:
  - AI Infrastructure
  - Systems
scenarios:
  - LLM Inference Serving
  - Distributed Inference
methods:
  - Iteration-Level Scheduling
  - Selective Batching
  - Pipeline Model Parallelism
doi: null
arxiv: null
official_url: "https://www.usenix.org/conference/osdi22/presentation/yu"
pdf_url: "https://www.usenix.org/system/files/osdi22-yu.pdf"
code_url: null
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The title, author list, venue, page range, and paper identity were verified against the official USENIX OSDI 2022 page and proceedings PDF."
  - "The complete 19-page proceedings PDF was inspected, including the system design, evaluation, related work, and references."
  - "No DOI, arXiv identifier, or official code repository was identified from the authoritative sources checked, so those fields remain null."
created: 2026-08-07
updated: 2026-08-13
---

# Orca: A Distributed Serving System for Transformer-Based Generative Models

## Learning Path Position

> **User-directed revision:** Recast the learning path as a three-layer architecture, grouped execution control and resource management as complementary runtime responsibilities, and marked this paper's focus within that layer.

The following is an analytical three-layer architecture for LLM inference
serving, ordered by widening system boundary rather than a strict dependency or
citation chain: one inference engine, one end-to-end service, and then the
cluster platform.

1. **Inference-engine runtime — this paper.** Make one logical serving engine
   execute concurrent requests efficiently through two complementary
   responsibilities:
   - **Request execution control — this paper's focus.** Decide how individual
     requests make progress and how runnable requests share each execution
     opportunity.
   - **Engine-local state and resource management.** Manage the state, memory,
     and local compute capacity needed to make those execution plans feasible.
2. **Service architecture and resource coordination.** Organize major serving
   components or stages and coordinate their resources against end-to-end
   performance objectives.
3. **Cluster platform orchestration.** Coordinate placement, load balancing,
   state movement, elasticity, and policy across serving instances.

Within the first layer, execution control decides who advances and when, while
resource management determines whether and how the required state and capacity
can be supplied. Neither responsibility sits above the other: scheduling
creates resource demand, and available capacity constrains scheduling. This
paper contributes primarily to execution control through iteration-level
scheduling and selective batching.

## Orca And Continuous Batching

> **User-directed revision:** Clarified Orca's relationship to the later continuous-batching terminology and its foundational contribution.

Orca calls its mechanism *iteration-level scheduling*: after each autoregressive
iteration, the scheduler can retire finished requests and admit waiting ones to
rebuild the next batch. Later systems generally call this behavior continuous,
in-flight, or iteration batching. Orca's foundational contribution was to make
this dynamic batch lifecycle a serving-system primitive and pair it with
selective batching so heterogeneous requests could still execute efficiently;
continuous batching names that scheduling lineage, not Orca's entire design or
selective batching alone.

## Summary

Orca reframes large generative-model serving around the unit that actually makes progress: one autoregressive iteration, not one whole request. Request-level batches force short generations to wait for the longest member and prevent newly arrived work from joining until the batch completes. Orca instead rebuilds the runnable batch at every iteration, immediately removes finished requests, and admits waiting requests as capacity becomes available.

That scheduling decision creates a second systems problem: requests have differently sized attention state, so not every operator can consume the same rectangular batch. Orca's selective batching keeps request-specific attention operations separate while batching the position-wise operators that share compatible tensor shapes. A distributed execution engine then runs this schedule across model-parallel workers, with control messages separated from high-volume tensor communication. The result is a foundational design for what later systems call continuous batching; in the paper's 175B-model experiment, Orca sustains 6.81 requests/s versus 0.185 requests/s for its FasterTransformer baseline at the reported 190 ms median normalized-latency point, a 36.9x throughput difference.

## Problem

Transformer-based generative models produce one token at a time and retain key/value attention state for all preceding tokens. A conventional serving engine forms a request-level batch and keeps it fixed until every request finishes. Because inputs and generated lengths vary, this design produces head-of-line blocking inside the batch: completed requests leave GPU capacity idle, long requests hold back short ones, and newly arrived requests wait even when some batch slots have become free.

Simply changing the scheduler is insufficient. At a given iteration, each request has a different sequence length and therefore a differently shaped attention state. Padding all requests to the same length wastes computation and memory, while executing every operator request by request sacrifices the GPU efficiency that batching is meant to provide. Orca addresses the scheduling and execution-shape problems together, then extends the design to model-parallel execution for models that do not fit on one GPU.

## Research Area And Scenario

> **User-directed revision:** Reclassified LLM as the workload/domain rather than a research area; retained AI Infrastructure and Systems as the primary areas.

- Area(s): AI Infrastructure and Systems.
- Scenario(s): Online autoregressive LLM inference, dynamic request batching, and distributed inference for models spanning multiple GPUs or servers.
- Why it matters here: The paper establishes the request-lifecycle abstraction underlying modern continuous-batching engines and shows how a scheduler must cooperate with attention-state handling and distributed execution.
- Indexing rationale: It belongs in AI Infrastructure and Systems because the contribution is a scheduler, execution engine, and communication design. LLM inference remains the workload context through the LLM Inference Serving scenario and LLM-specific tags and topics.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Requests execute the same Transformer-based generative model and advance through repeated autoregressive iterations.
    - Key constraint: **one-token-at-a-time generation with persistent attention state**.
    - Why it matters: Iteration-level scheduling relies on a natural synchronization point after each generated token.
    - Excludes or weakens: One-shot feed-forward inference and workloads whose execution cannot be safely paused or regrouped at iteration boundaries.
  - Claim: The engine can batch position-wise Transformer operators while handling attention separately for each request.
    - Key constraint: **operator-level control over the inference graph**.
    - Why it matters: Selective batching requires splitting the batch before attention and merging it afterward.
    - Excludes or weakens: Opaque runtimes that expose only whole-request execution or kernels that require every operator to use one fixed rectangular batch.
  - Claim: A request declares a maximum generation length through `max_tokens`.
    - Key constraint: **known upper bound on generated tokens**.
    - Why it matters: Orca reserves enough key/value-cache capacity before admitting a request, which avoids memory deadlock during later iterations.
    - Excludes or weakens: Unbounded generation or admission policies that cannot reserve against a declared maximum.
- Implied assumptions:
  - Claim: First-come-first-served admission is acceptable for the target service.
    - Key constraint: **single FCFS queue without explicit tenant priorities or deadlines**.
    - Why it matters: The scheduler fills each iteration from the request pool in arrival order rather than optimizing differentiated SLOs.
    - Excludes or weakens: Multi-tenant services requiring priority isolation, deadline scheduling, fairness weights, or per-class latency objectives.
  - Claim: The model graph resembles the dense GPT-style architectures evaluated in the paper.
    - Key constraint: **2022-era dense Transformer execution and a 2,048-token context limit**.
    - Why it matters: Selective batching and the measured attention/non-attention balance reflect those models and kernels.
    - Excludes or weakens: Direct quantitative extrapolation to grouped-query attention, mixture-of-experts routing, very long contexts, or newer fused-kernel stacks.
- What is ignored or abstracted away:
  - Claim: The evaluation abstracts away real model outputs and production arrival traces.
    - Key constraint: **synthetic Poisson arrivals and sampled token lengths**.
    - Why it matters: The experiment isolates systems behavior but does not reproduce burstiness, semantic early stopping, or correlated user sessions.
    - Excludes or weakens: Claims about production tail latency under adversarial or highly non-stationary traffic.
  - Claim: Cluster-level placement, replica autoscaling, fault tolerance, and cross-model routing are outside the design.
    - Key constraint: **one deployed model service with a fixed worker topology**.
    - Why it matters: Orca optimizes execution within that service rather than orchestrating a fleet of replicas.
    - Excludes or weakens: Multi-model serving platforms and elastic cluster-control problems.
- Applies when:
  - Claim: Request lengths vary enough that fixed request-level batches leave substantial capacity idle.
    - Key constraint: **heterogeneous input and output lengths under sustained load**.
    - Why it matters: Iteration-level admission can replace finished requests and reduce queueing behind long generations.
- May not apply when:
  - Claim: The service is lightly loaded, generations are nearly homogeneous, or another resource dominates.
    - Key constraint: **little opportunity to refill batches**.
    - Why it matters: Rebuilding schedules every iteration adds control complexity without a large utilization gain when batches rarely have holes.

## Core Idea

Treat each autoregressive iteration as a schedulable unit. At every token step, Orca forms a new batch from active and waiting requests, executes one iteration, retires completed requests, and repeats. To make that fine-grained schedule executable, selective batching batches the position-wise Transformer operators across requests but executes attention with each request's own sequence-shaped state.

## Method

> **User-directed revision:** Clarified selective batching's semantic-equivalence boundary, how K/V state follows hybrid model-parallel partitioning, and the pipeline execution unit.

Orca separates a control plane from a distributed execution engine. The scheduler owns the request pool, key/value-cache capacity accounting, and the per-iteration batch. It sends a schedule containing the requests and their current tokens to model workers. The engine executes the Transformer graph as a pipeline: compatible non-attention operators stay batched, attention is split into request-specific work, and outputs are merged before the next batched stage. Newly generated tokens and completion information return to the scheduler for the next iteration.

For large models, Orca partitions execution both within and across layers. Workers use model-parallel collectives for tensor data, while scheduler-to-worker control messages travel separately. This avoids routing high-volume intermediate tensors through the centralized scheduler and lets memory reservations and request bookkeeping remain centralized.

- Component: Iteration-level scheduler
  - Role: Continuously refill the running batch as requests arrive and finish.
  - Input: Waiting requests, active requests, each request's latest token and generation state, a maximum batch size, and available key/value-cache capacity.
  - Output: One iteration schedule containing the requests that may advance by one token.
  - Transformation: The scheduler admits requests in FCFS order, checks that each newly initiated request can reserve its declared maximum key/value-cache demand, fills up to the batch-size limit, and updates the request pool after the iteration completes.
  - Minimal example: If two requests in an eight-request batch finish, the next iteration can replace them with the two oldest waiting requests instead of waiting for the other six generations to end.
- Component: Selective batching engine
  - Role: Preserve GPU-efficient batching without padding every request's attention state to the same sequence length.
  - Input: A scheduled set of requests, their current token representations, and their differently sized attention key/value state.
  - Output: The next-token representations and updated attention state for every request in the iteration.
  - Transformation: Orca batches position-wise operators such as linear layers across tokens, splits the representation by request for attention, evaluates attention against each request's own state, and merges the results before subsequent batched operators.
  - Semantic boundary: With correct attention masks and position metadata, both padding-based alignment and Orca's split/merge execution preserve the same ideal mathematical result; changed batch shapes and fused FP16 kernels can still alter low-order bits, so bitwise-identical logits or sampled text are not guaranteed.
  - Minimal example: Two requests at sequence lengths 80 and 400 share one batched feed-forward operation, run attention against 80 and 400 positions separately, and then rejoin the batch.
- Component: Key/value state manager
  - Role: Track persistent attention state and prevent an admitted set of requests from exhausting memory mid-generation.
  - Input: Request identity, declared `max_tokens`, current sequence state, and worker memory capacity.
  - Output: Reserved state capacity and per-request key/value updates for later iterations.
  - Transformation: Capacity for a newly initiated request is checked and reserved before its first iteration; each subsequent iteration appends the new key/value vectors to that request's state.
  - Minimal example: A request declaring up to 128 output tokens is admitted only if its full future state can fit alongside already active reservations.
- Component: Distributed model executor
  - Role: Execute models too large for one GPU while following the centralized iteration schedule.
  - Input: The iteration schedule, input token IDs, partitioned model weights, and distributed key/value state.
  - Output: Sampled next tokens and updated distributed state.
  - Transformation: Orca combines intra-layer and inter-layer model parallelism, pipelines per-iteration batches across worker stages, uses NCCL for data-plane tensor communication, and uses separate control-plane messages for orchestration.
  - State placement (analysis): The logical key/value cache follows computation ownership: inter-layer workers retain only their assigned layers' state, while GPUs within an intra-layer partition retain only their local attention-head/QKV shard rather than a complete replica.
  - Minimal example: A 175B model is partitioned across 16 A100 GPUs; scheduled tokens move through pipeline stages while collectives synchronize layer shards without sending intermediate tensors through the scheduler.

## Experiments And Evidence

- **Orca engine vs. FasterTransformer engine → selective batching remains competitive before scheduler gains are counted.**
  - Selected evidence: Across 13B, 101B, and 175B configurations, the Orca engine is comparable to or faster than the FasterTransformer engine at tested batch sizes; for the 175B model the paper reports up to 47% lower execution time, attributed to control/data-plane separation.
  - Supports: Fine-grained execution does not erase its scheduling opportunity through overwhelming per-iteration overhead in the evaluated stack.
  - Original: Section 6.1 and Figure 9.
- **Full Orca vs. request-level FasterTransformer → iteration-level refill substantially increases sustainable throughput at a given latency.**
  - Selected evidence: For the 175B model, the paper highlights 6.81 requests/s for Orca versus 0.185 requests/s for FasterTransformer at a reported median normalized latency of 190 ms per generated token, a 36.9x throughput difference.
  - Supports: Fixed request-level batches were a severe utilization bottleneck in this experiment, and iteration-level scheduling converts freed slots into throughput.
  - Original: Section 6.2 and Figure 10.
- **Heterogeneous vs. homogeneous request lengths → variation is important but is not the only source of gain.**
  - Selected evidence: Orca retains a throughput/latency advantage in the homogeneous-length trace, though the advantage over request-level batching is smaller than under heterogeneous lengths.
  - Supports: Removing batch holes helps even without broad length variance, while heterogeneous completion times amplify the opportunity.
  - Original: Section 6.3 and Figure 11.
- **Evaluation boundary.**
  - Selected evidence: The experiments use Azure ND96asr A100 v4 machines with eight 40 GB A100 GPUs each, models from 13B to 341B parameters, a maximum sequence length of 2,048, uniformly sampled input/output lengths, and Poisson arrivals. The generator is configured to run for the sampled maximum rather than produce semantic EOS behavior.
  - Supports: The results establish the mechanism under controlled large-model serving workloads, not an empirical guarantee for current production traffic or modern model architectures.
  - Original: Section 6 and the workload description in Section 6.2.

## Contributions

- Introduces iteration-level scheduling for generative Transformer serving, allowing a dynamic request set at every token step.
- Designs selective batching so requests with differently shaped attention state can still share efficient batched non-attention computation.
- Builds a distributed serving system that coordinates model-parallel execution while separating control-plane and data-plane communication.
- Demonstrates the end-to-end effect across models from 13B to 341B parameters and establishes a systems foundation used by later LLM serving engines.

## Limitations

- The traffic trace is synthetic: input/output lengths are sampled, arrivals are Poisson, and semantic generation or EOS behavior is not exercised.
- Admission reserves key/value-cache space up to each request's declared maximum generation length. This prevents deadlock but can waste memory and directly motivates later block-based allocation systems.
- The largest headline gain is against a request-level FasterTransformer service built for the paper's evaluation; it should not be read as a comparison with later continuous-batching engines.
- Models, kernels, context lengths, and A100-era hardware reflect the 2022 serving stack; the quantitative balance can change with grouped-query attention, longer contexts, newer GPUs, and fused kernels.
- The paper optimizes one model service with a fixed worker topology and does not address replica-level load balancing, phase disaggregation, autoscaling, failure recovery, or differentiated tenant SLOs.

## Key Takeaways

- For autoregressive inference, the request is often too coarse a scheduling unit; the token iteration exposes reusable capacity much earlier.
- Continuous batching is an execution-interface problem as well as a queueing policy: attention state shapes must be handled without forcing the whole graph into padding.
- Orca's full-state reservation makes scheduling safe but memory-inefficient, which explains why PagedAttention is a natural next paper.
- Headline throughput gains are meaningful only together with the latency definition, baseline scheduler, workload distribution, model size, and memory policy.

## Relation: Previous Work

- Title: TensorFlow-Serving: Flexible, High-Performance ML Serving
  Authors: Christopher Olston; Noah Fiedel; Kiril Gorovoy; Jeremiah Harmsen; Li Lao; Fangwei Li; Vinu Rajashekhar; Sukriti Ramesh; Jordan Soyke.
  URL: https://arxiv.org/abs/1712.06139
  - Type: system
  - Status: not-ingested
  - Work summary: TensorFlow Serving provides a general production framework for deploying and managing learned models.
  - Role: Represents the general model-serving lineage that handles versions and inference requests but is not specialized for autoregressive token generation.
  - Limitation: Its general abstraction does not expose per-token iteration scheduling or persistent request-specific attention state.
  - This paper: Specializes the serving loop around autoregressive progress and model-parallel generative execution.

## Relation: Compared With

- Title: Efficient Memory Management for Large Language Model Serving with PagedAttention
  Authors: Woosuk Kwon; Zhuohan Li; Siyuan Zhuang; Ying Sheng; Lianmin Zheng; Cody Hao Yu; Joseph E. Gonzalez; Hao Zhang; Ion Stoica.
  URL: https://dl.acm.org/doi/10.1145/3600006.3613165
  Obsidian: [[kwon2023pagedattention]]
  - Type: compares-with
  - Status: ingested
  - Work summary: vLLM retains iteration-level request interleaving but replaces contiguous, maximum-length key/value-cache reservations with paged, on-demand blocks and sharing.
  - Similarity: Both systems dynamically batch autoregressive requests and target higher GPU utilization at controlled latency.
  - Difference: Orca contributes the scheduling and selective-batching foundation; vLLM targets the memory-capacity bottleneck that remains under that scheduler.
  - When to use which: Read Orca to understand the request execution lifecycle; use vLLM's abstraction when key/value-cache fragmentation or sharing limits the number of concurrent requests.
- Title: FasterTransformer
  Authors: NVIDIA.
  URL: https://github.com/NVIDIA/FasterTransformer
  - Type: compares-with
  - Status: not-ingested
  - Work summary: FasterTransformer is an optimized GPU inference library for Transformer models and provides the execution baseline used in Orca's evaluation.
  - Similarity: Both execute large Transformer models with GPU and model-parallel optimizations.
  - Difference: The evaluated FasterTransformer service uses request-level batching, while Orca adds iteration-level scheduling, selective batching, and its own distributed control plane.
  - When to use which: Use a kernel library when optimized operators are the main need; use an Orca-style serving loop when dynamic autoregressive scheduling is the bottleneck.

## Source Notes

- Metadata sources:
  - Official USENIX paper page: https://www.usenix.org/conference/osdi22/presentation/yu
  - Official USENIX proceedings PDF: https://www.usenix.org/system/files/osdi22-yu.pdf
  - DBLP bibliographic record: https://dblp.org/rec/conf/osdi/YuJKKC22
- Source verification: The complete official proceedings PDF was inspected directly, including the architecture, scheduling algorithm, evaluation setup and figures, related work, and references. The first page and representative evaluation figures were also visually checked against the extracted text.
- PDF policy: The PDF was not added to the vault or repository. This note records only public source URLs and no local filesystem path.

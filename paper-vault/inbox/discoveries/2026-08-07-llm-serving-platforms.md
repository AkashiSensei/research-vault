---
type: paper-intake-history
status: completed
topic: "LLM 在线推理平台的调度、内存与阶段资源管理"
research_lanes: ["AI Infra"]
intake_run_at: "2026-08-07T12:25:04+08:00"
created: 2026-08-07
updated: 2026-08-07
---

# Paper Intake: LLM 在线推理平台的调度、内存与阶段资源管理

## Direction Decision

本次没有临时引导；稳定配置同时关注 Agent 与 AI Infra。现有规范笔记中，Agent 已有一篇领域综述和 ReAct 机制论文，AI Infra 只有一篇面向长上下文微调的 JENGA，尚未覆盖配置中优先级更高的平台问题，尤其是在线推理中的请求调度、内存管理与资源编排。最近窗口只有 1 次有效 intake，仍处于 warm-up，因此历史平衡只作为弱提示；真正驱动本次选择的是明确的平台偏好、现有知识缺口，以及用户已有训练与推理基础、适合继续学习系统论文如何把工程瓶颈抽象为科研问题。

本批次聚焦“LLM 在线推理平台如何在动态请求下联合管理执行调度、KV cache 与不同推理阶段的资源，以同时改善吞吐、延迟和可预测性”。预期用奠基调度机制建立请求生命周期模型，再读代表性的内存管理系统，最后比较阶段拆分、动态迁移或调度优化等后续路线；这组角色能把工程现象、系统机制和端到端评测串成一条学习路径。上一批遗留的 Toolformer、Reflexion 与 AgentBench 仍是可信的 Agent 候选，但它们与本批平台主题不相连，也会延续上一批方向，因此本次暂缓而非排除。

## Learning Goal And Reading Path

- 学习问题：在线 LLM 服务系统如何识别调度、KV cache 和 prefill/decode 资源干扰这三类瓶颈，并通过系统机制与实验设计证明端到端收益及适用边界？
- 阅读路径：奠基调度模型 → KV cache/内存管理代表系统 → 阶段与资源编排的后续路线 → 对比各系统的负载假设、延迟目标和评测证据。
- 历史窗口：检查了最近 1 次有效 intake；少于 7 次，属于 warm-up，历史仅作弱平衡依据。

## Paper Decisions

### 1. [Efficient Memory Management for Large Language Model Serving with PagedAttention](https://dl.acm.org/doi/10.1145/3600006.3613165)

- Status: ingested
- Role: representative foundation
- Lane: AI Infra
- Subarea: KV cache memory management
- Core: PagedAttention maps dynamically growing KV caches onto fixed-size non-contiguous blocks, while vLLM co-designs block management and request scheduling to reduce fragmentation, enable sharing, and admit larger batches.
- Why today: It turns a concrete GPU-memory failure mode into a reusable systems abstraction and supplies the memory substrate used or assumed by later serving platforms.
- Relation: Directly cites Orca's iteration-level scheduling as a complementary foundation; Sarathi-Serve, DistServe, and Llumnix use it as a baseline, implementation substrate, or architectural point of departure.
- Handoff: [[paper-vault/papers/kwon2023pagedattention]]

### 2. [Orca: A Distributed Serving System for Transformer-Based Generative Models](https://www.usenix.org/conference/osdi22/presentation/yu)

- Status: ingested
- Role: foundational
- Lane: AI Infra
- Subarea: iteration-level scheduling
- Core: Orca replaces request-level static batching with iteration-level scheduling and selective batching so finished requests can leave and new requests can join an autoregressive generation batch.
- Why today: It supplies the request-lifecycle and scheduling model needed to understand why later KV-cache, prefill/decode, and migration systems exist.
- Relation: Verified prerequisite cited by vLLM, Sarathi-Serve, DistServe, and Llumnix; vLLM adds complementary memory management to Orca's scheduling abstraction.
- Handoff: [[paper-vault/papers/yu2022orca]]

### 3. [Taming Throughput-Latency Tradeoff in LLM Inference with Sarathi-Serve](https://www.usenix.org/conference/osdi24/presentation/agrawal)

- Status: recommended
- Role: representative advance
- Lane: AI Infra
- Subarea: chunked prefill and latency-aware scheduling
- Core: Sarathi-Serve splits long prefills into chunks and forms stall-free, more uniform batches so ongoing decodes can retain low inter-token latency without giving up serving capacity.
- Why today: It exposes how a production-facing latency SLO changes the scheduler built on top of iteration-level batching and paged memory.
- Relation: Verified descendant of Orca/vLLM-style iteration batching; analytically contrasts co-located chunking with DistServe's phase disaggregation.
- Handoff: Legal full text: https://www.usenix.org/system/files/osdi24-agrawal.pdf ; reconsider after the foundational pair or in a latency-SLO-focused batch.

### 4. [DistServe: Disaggregating Prefill and Decoding for Goodput-optimized Large Language Model Serving](https://www.usenix.org/conference/osdi24/presentation/zhong-yinmin)

- Status: recommended
- Role: contrasting advance
- Lane: AI Infra
- Subarea: phase disaggregation and resource planning
- Core: DistServe assigns prefill and decode to separate GPU pools, then co-optimizes per-phase parallelism, allocation, and placement for joint TTFT and TPOT targets.
- Why today: It turns phase interference and coupled provisioning into an explicit cluster-resource research problem and provides the strongest architectural contrast to Sarathi-Serve.
- Relation: Cites Orca and vLLM as co-located predecessors; contrasts analytically with Sarathi-Serve's stall-free co-location and connects to Llumnix at the cluster orchestration layer.
- Handoff: Legal full text: https://www.usenix.org/system/files/osdi24-zhong-yinmin.pdf ; reconsider in a phase-disaggregation or SLO-goodput batch.

### 5. [Llumnix: Dynamic Scheduling for Large Language Model Serving](https://www.usenix.org/conference/osdi24/presentation/sun-biao)

- Status: recommended
- Role: bridge to cluster orchestration
- Lane: AI Infra
- Subarea: multi-instance rescheduling and live migration
- Core: Llumnix migrates requests and their in-memory states across model instances, using distributed scheduling and virtual usage to react to imbalance, fragmentation, priorities, and autoscaling.
- Why today: It lifts the batch from one engine or one phase plan to dynamic multi-instance platform management under heterogeneous, unpredictable workloads.
- Relation: Directly uses vLLM as an underlying engine and cites Orca/vLLM; its relation to Sarathi-Serve and DistServe is an evidence-backed analytical comparison rather than claimed lineage.
- Handoff: Legal full text: https://www.usenix.org/system/files/osdi24-sun-biao.pdf ; reconsider in a cluster scheduling, isolation, or autoscaling batch.

## Future Handoff

- 本次优先补齐 AI Infra 平台与在线推理服务覆盖，不把 Agent 与 AI Infra 混成一个批次。
- Toolformer、Reflexion 与 AgentBench 是上一批仍未摄入的强候选；它们因本次主题不匹配而暂缓，后续 Agent 批次可重新核验并与新候选竞争，不保留固定名额。
- 本次五篇形成“Orca 的迭代级调度 → vLLM 的 KV-cache 内存管理 → Sarathi-Serve 的分块 prefill / DistServe 的阶段解耦 → Llumnix 的跨实例重调度”连通图。
- 本次已摄入 Orca 与 vLLM，补齐了迭代级调度、选择性批处理、分页 KV-cache 内存管理及其直接引用关系；后续平台论文可把这两篇视为已覆盖前置。
- Sarathi-Serve、DistServe 与 Llumnix 因本轮两篇自动笔记上限而保留推荐，不是质量或来源淘汰；它们应在未来批次与届时的新候选重新竞争，不预留固定名额。
- 元数据 caveat：OpenAlex 对正式版与预印本存在未合并记录；DistServe 引用的是 Sarathi 的早期论文，而不是最终 OSDI Sarathi-Serve，后续不得把两个 arXiv 标识混用。

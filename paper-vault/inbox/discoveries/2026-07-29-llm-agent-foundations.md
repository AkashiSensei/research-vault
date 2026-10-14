---
type: paper-discovery
status: active
topic: "LLM 自主智能体的核心架构、能力机制与评测基础"
requirement_sources:
  - paper-vault/paper-intake-config.md
recommendation_count: 5
intake_run_at: "2026-07-29T14:44:04+08:00"
created: 2026-07-29
updated: 2026-07-29
---

# Paper Discovery: LLM 自主智能体的核心架构、能力机制与评测基础

## Search Brief

- Batch Theme: LLM 自主智能体的核心架构、能力机制与评测基础。
- Batch Learning Goal: 回答“一个 LLM 如何从语言模型变成能够自主行动的 Agent，以及这些能力应当如何评测？”。
- Requested set: 5 篇候选论文，最多自动摄入 2 篇。
- Stable preference: Agent 与 AI Infra；Agent 当前处于入门阶段，优先综述、taxonomy、奠基工作和主要分支代表作。
- Temporary guidance: 无。
- Explicit exclusions: 无。
- Evidence gate: 推荐至少达到中等证据可信度；生成笔记前必须阅读全文；仅有摘要的论文可以保留推荐，但不能摄入。
- Vault context: 当前唯一的规范论文笔记是 AI Infra 方向的 [[paper-vault/papers/wang2025jenga]]，尚无 Agent 方向的规范笔记。
- Relation pattern: 先用综述建立架构地图，再用推理与行动、工具学习、自我改进和评测基准四类代表作展开；优先使用已核验的引用或演进关系，必要时使用有原始证据支持的分析性对比。

## Rolling Balance Snapshot

- Intake runs inspected: 0 of the most recent 14.
- Warm-up window: yes; fewer than 7 prior intake runs are available, so no strong historical-balance claim is made.
- Ingested counts in the run window:
  - Research lane: none recorded.
  - Subarea: none recorded.
  - Paper role: none recorded.
  - Batch theme: none recorded.
- Carry-over candidates reconsidered: 0.
- Balance effect: because the intake history is empty and the existing vault has one AI Infra note but no Agent note, the run begins the configured Agent learning map with one coherent foundation-and-evaluation batch. This is a warm-up choice, not a quota.

## Set Coherence

- Mode: coherent batch.
- Shared question: What architectural capabilities turn an LLM into an autonomous agent, how can those capabilities be acquired, and how should the resulting agent be evaluated?
- Coverage strategy: one field-map survey plus representative works for reasoning-and-acting, learned tool use, verbal self-improvement, and multi-environment evaluation.
- Connection map:
  1. A survey on large language model based autonomous agents -> ReAct: Synergizing Reasoning and Acting in Language Models — architecture-map to foundational mechanism; verified direct citation in the survey.
  2. A survey on large language model based autonomous agents -> Toolformer: Language Models Can Teach Themselves to Use Tools — architecture-map to learned tool use; verified direct citation in the survey.
  3. A survey on large language model based autonomous agents -> Reflexion: Language Agents with Verbal Reinforcement Learning — architecture-map to memory and self-improvement; verified direct citation in the survey.
  4. A survey on large language model based autonomous agents -> AgentBench: Evaluating LLMs as Agents — architecture-map to evaluation; verified direct citation in the survey.
  5. ReAct: Synergizing Reasoning and Acting in Language Models -> Reflexion: Language Agents with Verbal Reinforcement Learning — mechanism to later extension; verified lineage in Reflexion's primary record.
  6. ReAct: Synergizing Reasoning and Acting in Language Models <-> Toolformer: Language Models Can Teach Themselves to Use Tools — prompt-time interaction versus train-time API-use acquisition; evidence-backed analytical comparison from the primary papers.

## Recommended Papers

### 1. A survey on large language model based autonomous agents

- Status: ingested
- Authors: Lei Wang; Chen Ma; Xueyang Feng; Zeyu Zhang; Hao Yang; Jingsen Zhang; Zhiyuan Chen; Jiakai Tang; Xu Chen; Yankai Lin; Wayne Xin Zhao; Zhewei Wei; Jirong Wen
- Year: 2024
- Venue: Frontiers of Computer Science
- Work type: journal review article
- Official page: https://link.springer.com/article/10.1007/s11704-024-40231-1
- Full text: https://link.springer.com/content/pdf/10.1007/s11704-024-40231-1.pdf
- Identifiers: DOI 10.1007/s11704-024-40231-1; arXiv 2308.11432
- Discovery path: broad survey search, then publisher and arXiv identity cross-check
- Research lane: Agent
- Subarea: field taxonomy and architecture
- Paper role: survey
- Paper summary: The review organizes early LLM-agent research around construction, applications, and evaluation. Its central synthesis is a profile–memory–planning–action architecture, paired with a taxonomy of capability acquisition through fine-tuning, prompting, or surrounding mechanisms.
- Set relationship: Provides the common map for ReAct, Toolformer, Reflexion, and AgentBench; all four connections are verified direct citations.
- Requirement fit: Directly serves the configured beginner goal of learning the field's questions, representative routes, and their relationships before following frontier work.
- Venue signal: Peer-reviewed open-access review article in Frontiers of Computer Science; no preferred venue list or ranking requirement was configured.
- Impact signal: 1,292 citations in the OpenAlex DOI record, checked 2026-07-29; the Springer page displayed a higher publisher count, so the value is source- and version-sensitive.
- Quality and innovation: Offers a legible unified vocabulary and maps roughly 100 early works, but does not disclose a reproducible systematic-review search or screening protocol.
- Applicability: Best used as an orientation map for the 2021–August 2023 wave of LLM agents, not as a current or quantitatively exhaustive review.
- Vault relationship: Starts the previously empty Agent lane and complements the existing AI Infra note [[paper-vault/papers/wang2025jenga]] without duplicating it.
- Recommendation reason: It is the strongest anchor for turning four mechanisms and benchmarks into a learning path rather than a list of unrelated papers.
- Caveats: The effective literature horizon is mostly August 2023; its taxonomy is descriptive, several categories overlap, and primary records should be used to verify individual cited works.
- Evidence confidence: high
- Relative score: 93/100

### 2. ReAct: Synergizing Reasoning and Acting in Language Models

- Status: ingested
- Authors: Shunyu Yao; Jeffrey Zhao; Dian Yu; Nan Du; Izhak Shafran; Karthik Narasimhan; Yuan Cao
- Year: 2023
- Venue: The Eleventh International Conference on Learning Representations
- Work type: conference full paper
- Official page: https://openreview.net/forum?id=WE_vluYUL-X
- Full text: https://arxiv.org/pdf/2210.03629
- Identifiers: arXiv 2210.03629; OpenReview WE_vluYUL-X
- Discovery path: backward citation from the survey, then OpenReview, arXiv, DBLP, project, and code cross-check
- Research lane: Agent
- Subarea: reasoning and acting
- Paper role: foundational
- Paper summary: ReAct prompts a frozen language model to interleave natural-language thoughts with task-specific actions and to feed textual observations back into subsequent reasoning. It evaluates the loop on Wikipedia-grounded QA and fact verification plus the ALFWorld and WebShop text environments.
- Set relationship: Instantiates the survey's feedback-conditioned planning loop, contrasts analytically with Toolformer's learned API calls, and provides the reasoning/action substrate extended by Reflexion.
- Requirement fit: Supplies a concrete foundational mechanism immediately after the field map and exposes how planning, tool use, environment feedback, and evaluation connect.
- Venue signal: ICLR 2023 conference paper; no configured venue ranking preference.
- Impact signal: unknown; the current OpenAlex work record returned for the arXiv identifier had mismatched bibliographic content, so no citation count is reported.
- Quality and innovation: Introduces a simple, reusable thought–action–observation protocol and evaluates it across four text tasks with reason-only and action-only ablations, prompt permutations, hybrids, and manual failure analysis.
- Applicability: Useful when a capable LLM can operate through a well-defined textual action interface and demonstrations fit in context.
- Vault relationship: Establishes the first mechanism-level Agent note and has no overlap with [[paper-vault/papers/wang2025jenga]] beyond both using LLMs.
- Recommendation reason: It turns the survey's modular description into an inspectable agent loop and is a prerequisite for understanding later reflection and tool-agent work.
- Caveats: Main results use frozen PaLM-540B and hand-designed task interfaces; plain ReAct is not uniformly best on knowledge tasks, several headline results use best-of-prompt reporting, and the benchmarks are restricted text sandboxes.
- Evidence confidence: high
- Relative score: 92/100

### 3. Toolformer: Language Models Can Teach Themselves to Use Tools

- Status: recommended
- Authors: Timo Schick; Jane Dwivedi-Yu; Roberto Dessì; Roberta Raileanu; Maria Lomeli; Eric Hambro; Luke Zettlemoyer; Nicola Cancedda; Thomas Scialom
- Year: 2023
- Venue: Advances in Neural Information Processing Systems 36
- Work type: conference full paper
- Official page: https://proceedings.neurips.cc/paper_files/paper/2023/hash/d842425e4bf79ba039352da0f658a906-Abstract-Conference.html
- Full text: https://proceedings.neurips.cc/paper_files/paper/2023/file/d842425e4bf79ba039352da0f658a906-Paper-Conference.pdf
- Identifiers: DOI 10.52202/075280-2997; arXiv 2302.04761
- Discovery path: backward citation from the survey, then NeurIPS, arXiv, and DBLP cross-check
- Research lane: Agent
- Subarea: tool use
- Paper role: representative
- Paper summary: Toolformer creates self-supervised API-call annotations, filters them by whether returned tool results improve language-model likelihood, and fine-tunes the model to decide which tool to call, when, and with what arguments. It studies calculators, search, translation, calendar, and question-answering tools.
- Set relationship: Complements ReAct by acquiring API use in model parameters rather than relying chiefly on prompt-time interleaved reasoning; the survey directly cites it as an external-tool action system.
- Requirement fit: Covers the configured tool-use branch with a mechanism that is distinct from prompt orchestration.
- Venue signal: NeurIPS 2023 conference full paper; no configured venue ranking preference.
- Impact signal: 395 citations in the OpenAlex arXiv work record, checked 2026-07-29; version splitting may affect the count.
- Quality and innovation: Converts tool-use supervision into an automated data-generation and filtering problem and tests multiple APIs, offering a clear alternative to manually authored demonstrations.
- Applicability: Most useful when tool APIs can be represented as text spans and enough unlabeled text plus model-training access are available.
- Vault relationship: Adds a future tool-learning branch to the new Agent map and does not duplicate an existing canonical note.
- Recommendation reason: It creates a clean conceptual contrast with ReAct and prevents “tool use” from being learned only as prompt-time action syntax.
- Caveats: The tool set and invocation format are fixed, the model must be trainable, and the recommendation is grounded in official metadata and primary abstract/full-text availability rather than a full-text ingestion pass in this run.
- Evidence confidence: medium
- Relative score: 88/100

### 4. Reflexion: Language Agents with Verbal Reinforcement Learning

- Status: recommended
- Authors: Noah Shinn; Federico Cassano; Ashwin Gopinath; Karthik Narasimhan; Shunyu Yao
- Year: 2023
- Venue: Advances in Neural Information Processing Systems 36
- Work type: conference full paper
- Official page: https://proceedings.neurips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html
- Full text: https://proceedings.neurips.cc/paper_files/paper/2023/file/1b44b878bb782e6954cd888628510e90-Paper-Conference.pdf
- Identifiers: DOI 10.52202/075280-0377; arXiv 2303.11366
- Discovery path: backward citation from the survey, then NeurIPS, arXiv, and DBLP cross-check
- Research lane: Agent
- Subarea: memory and self-improvement
- Paper role: representative
- Paper summary: Reflexion converts task feedback into verbal reflections stored in episodic memory so a language agent can alter later trials without updating model weights. It evaluates sequential decision making, programming, and reasoning tasks.
- Set relationship: Extends ReAct-style agents with feedback, reflection, and memory; the survey directly connects it to memory and feedback-conditioned planning.
- Requirement fit: Covers the configured learning and self-improvement branch while remaining connected to the foundational reasoning/action loop.
- Venue signal: NeurIPS 2023 conference full paper; no configured venue ranking preference.
- Impact signal: 272 citations in the OpenAlex arXiv work record, checked 2026-07-29; version splitting may affect the count.
- Quality and innovation: Makes verbalized feedback a persistent control signal and evaluates a no-weight-update improvement loop across several task families.
- Applicability: Useful when a task supplies meaningful success signals or feedback and repeated attempts are permitted.
- Vault relationship: Extends the proposed Agent map from one-shot interaction to experience-based iteration; no canonical duplicate exists.
- Recommendation reason: It is the most direct bridge from the survey's memory/planning taxonomy to an operational self-improvement mechanism.
- Caveats: Benefits depend on feedback quality and repeated trials; episodic verbal memory can inherit model errors, and this paper was not chosen for full-text note generation in this cycle.
- Evidence confidence: medium
- Relative score: 87/100

### 5. AgentBench: Evaluating LLMs as Agents

- Status: recommended
- Authors: Xiao Liu; Hao Yu; Hanchen Zhang; Yifan Xu; Xuanyu Lei; Hanyu Lai; Yu Gu; Hangliang Ding; Kaiwen Men; Kejuan Yang; Shudan Zhang; Xiang Deng; Aohan Zeng; Zhengxiao Du; Chenhui Zhang; Sheng Shen; Tianjun Zhang; Yu Su; Huan Sun; Minlie Huang; Yuxiao Dong; Jie Tang
- Year: 2024
- Venue: The Twelfth International Conference on Learning Representations
- Work type: conference full paper
- Official page: https://openreview.net/forum?id=zAdUB0aCTQ
- Full text: https://openreview.net/pdf?id=zAdUB0aCTQ
- Identifiers: arXiv 2308.03688; OpenReview zAdUB0aCTQ
- Discovery path: backward citation from the survey, then OpenReview, arXiv, DBLP, and project cross-check
- Research lane: Agent
- Subarea: evaluation
- Paper role: bridge
- Paper summary: AgentBench evaluates language models as interactive agents across eight environments spanning operating systems, databases, knowledge graphs, games, household tasks, and web shopping. It emphasizes long-horizon reasoning, decision making, and instruction following under environment feedback.
- Set relationship: Supplies the multi-environment evaluation branch named by the survey and provides a common empirical lens for mechanism papers such as ReAct.
- Requirement fit: Directly covers the configured Agent-evaluation priority and closes the batch with evidence rather than architecture alone.
- Venue signal: ICLR 2024 conference full paper; no configured venue ranking preference.
- Impact signal: 53 citations in the OpenAlex arXiv work record, checked 2026-07-29; interpret the lower count with age and version caveats.
- Quality and innovation: Broadens agent evaluation beyond a single tool or environment and documents large performance gaps between strong language modeling and reliable interaction.
- Applicability: Useful for comparing general-purpose text agents across heterogeneous interactive tasks and for identifying environment-specific weaknesses.
- Vault relationship: Would add the first dedicated Agent benchmark note; no canonical duplicate exists.
- Recommendation reason: It prevents the learning path from ending at mechanisms and makes evaluation design a first-class research problem.
- Caveats: Environment coverage is broad but not equivalent to open-world deployment, benchmark adapters and prompt design can affect results, and this paper was not chosen for full-text note generation in this cycle.
- Evidence confidence: medium
- Relative score: 85/100

## Suggested Order

1. A survey on large language model based autonomous agents — establish the shared vocabulary for profiles, memory, planning, action, capability acquisition, applications, and evaluation.
2. ReAct: Synergizing Reasoning and Acting in Language Models — instantiate the survey's feedback-conditioned planning and action loop.
3. Toolformer: Language Models Can Teach Themselves to Use Tools — contrast prompt-time interaction with train-time acquisition of API use.
4. Reflexion: Language Agents with Verbal Reinforcement Learning — add persistent feedback, memory, and iterative self-improvement to the ReAct lineage.
5. AgentBench: Evaluating LLMs as Agents — finish by asking how heterogeneous agent abilities should be measured across environments.

## Search And Exclusion Summary

- Candidate pool: 10 distinct paper families in the final comparative screen.
- Removed as already ingested: 0.
- Removed by explicit exclusions: 0.
- Collapsed version duplicates: 5 publisher/venue and arXiv representations.
- Rejected by hard constraints: 0.
- Insufficiently verified: 0.
- Qualified but not retained in the final five: 5.
- Final recommendations: 5.
- Meaningful near-misses:
  - Augmented Language Models: a Survey overlaps the selected survey and is narrower around reasoning and tools.
  - Generative Agents: Interactive Simulacra of Human Behavior is important for memory and social simulation but would pull this first batch toward one application branch.
  - CAMEL: Communicative Agents for “Mind” Exploration of Large Scale Language Model Society belongs in a later multi-agent batch.
  - Voyager: An Open-Ended Embodied Agent with Large Language Models belongs in a later embodied learning batch.
  - The Rise and Potential of Large Language Model Based Agents substantially overlaps the selected field-map role.

## Sources And Freshness

- Requirement documents read: `paper-vault/paper-intake-config.md`; no supplementary brief was named.
- Vault deduplication source: canonical notes under `paper-vault/papers/`, inspected before search.
- Search and verification services: built-in Web search, arXiv, DBLP, official Springer, OpenReview and NeurIPS records, and OpenAlex API records.
- Full-text availability was checked against legal publisher, venue, or arXiv links. The two selected papers were acquired outside the repository and read completely before ingestion.
- Citation source: OpenAlex API, observed 2026-07-29. Citation counts are secondary, version-sensitive signals rather than quality guarantees; ReAct's count was left unknown because the returned work record was bibliographically inconsistent.
- Search date: 2026-07-29.
- Coverage limitations: the preferred AnySearch endpoint was attempted but failed because its available API did not expose the required domain-list operation. OpenReview's interactive page was not relied on for ReAct full text; its arXiv camera-ready PDF was used instead. No venue ranking was applied because the configuration names no preferred venue or ranking system.

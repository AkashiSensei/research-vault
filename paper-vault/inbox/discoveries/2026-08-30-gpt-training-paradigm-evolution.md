---
type: paper-intake-history
status: completed
topic: "GPT 训练范式从生成式预训练到对齐的演进"
research_lanes: ["LLM"]
intake_run_at: "2026-08-30T09:05:46+08:00"
created: 2026-08-30
updated: 2026-08-30
---

# Paper Intake: GPT 训练范式从生成式预训练到对齐的演进

## Direction Decision

当前配置昨天更新的第一项临时引导明确要求：下一次进入 LLM 方向时，以 GPT 模型论文为主题，并在来源与质量门槛满足时优先摄入 Radford et al. (2018) 的 *Improving Language Understanding by Generative Pre-Training*。仓库刚完成原始 Transformer 与 BERT，已经具备注意力架构和 encoder-only 双向预训练的前置，但 causal decoder-only 生成式预训练仍只有上一轮的推荐记录，没有规范笔记；因此本次应把注意力从“Transformer 部件是什么”推进到“GPT 如何改变任务迁移与能力获取方式”。最近仅有 3 次可识别 intake，Agent、AI Infra 与 LLM 各完成一个批次，仍属 warm-up，历史平衡只说明不存在迫切失衡，不是本次选题的主要依据。

本批围绕“GPT 直接谱系如何从生成式预训练加任务微调，演进到零样本/上下文学习，再到人类反馈对齐”组织。预期角色依次是：用 GPT-1 建立 causal decoder-only 预训练、任务输入变换与端到端微调的基线；用后续直接谱系中的代表论文观察规模化后任务适配如何从参数更新转向提示内推断；再用 instruction following 与人类反馈工作解释为何仅扩大基础模型并不足以获得稳定、符合用户意图的行为。这样的组合既能与 [[paper-vault/papers/devlin2019bert]] 形成明确的双向编码/因果解码与微调目标对比，又能在 GPT 内部形成一条连通训练范式，而不是泛化成不受约束的“大模型史”。

AI Infra 的 prefill/decode 协同与跨实例编排是最强备选：Sarathi-Serve、DistServe 与 Llumnix 都曾通过推荐门槛，且能延续 [[paper-vault/papers/yu2022orca]] 与 [[paper-vault/papers/kwon2023pagedattention]]。本次暂缓它，是因为新写入配置的 GPT 临时引导更具体、GPT-1 已有经过核验的 carry-over 来源、现有 Transformer/BERT 前置使这一学习窗口正合适；这些系统候选仍可在后续批次与新工作公平竞争，不预留名额。Agent 架构 taxonomy 的时效性复核同样保留为后续主题，本次不与 GPT 混合。

## Learning Goal And Reading Path

- 学习问题：GPT 直接谱系如何改变预训练目标、任务接口与适配信号，使能力获取从有监督微调逐步转向零/少样本上下文学习与基于人类反馈的行为对齐？
- 阅读路径：GPT-1 的 causal decoder-only 生成式预训练与任务微调基线 → 规模化带来的零样本与上下文学习 → instruction tuning / human feedback 对齐；用 BERT 只作已摄入的对照前置，不把 encoder-only 分支混入候选集。
- 历史窗口：检查了最近 3 次可识别 intake；少于 7 次，属于 warm-up，历史平衡与 carry-over 仅作弱背景和候选种子依据。

## Paper Decisions

### 1. [Improving Language Understanding by Generative Pre-Training](https://openai.com/index/language-unsupervised/)

- Status: ingested
- Role: foundational pre-training and fine-tuning baseline
- Lane: LLM
- Subarea: causal language-model pre-training and supervised transfer
- Core: The report pre-trains a decoder-only causal Transformer on BooksCorpus, converts structured downstream inputs into token sequences, and fine-tunes the full model with small task heads.
- Why today: It is the configuration's explicitly prioritized paper and supplies the missing causal-decoder counterpart to the already ingested BERT encoder.
- Relation: It directly adapts the original Transformer and is the verified predecessor that GPT-2 scales; its bidirectional-versus-causal and objective differences with BERT form an evidence-backed comparison.
- Handoff: Ingested as [[paper-vault/papers/radford2018improving]] from the complete official OpenAI PDF and indexed under its verified venue, scenario, topic, method, and dataset facets.

### 2. [Language Models are Unsupervised Multitask Learners](https://openai.com/index/better-language-models/)

- Status: ingested
- Role: direct scale-up and zero-shot transfer bridge
- Lane: LLM
- Subarea: web-scale causal language modeling and zero-shot task transfer
- Core: GPT-2 scales the same causal next-token objective to WebText and a 1.5B-parameter model, then elicits downstream behavior through natural-language context without task-specific parameter updates.
- Why today: It is the nearest verified successor to GPT-1 and exposes the first clean shift from end-to-end supervised fine-tuning toward inference-time task specification.
- Relation: The report says its architecture largely follows GPT and evaluates the smallest model at original-GPT scale; GPT-3 then explicitly retains GPT-2's architecture while expanding in-context learning.
- Handoff: Ingested as [[paper-vault/papers/radford2019language]] from the complete official OpenAI PDF and indexed under its verified venue, scenario, topic, method, and dataset facets.

### 3. [Language Models are Few-Shot Learners](https://proceedings.neurips.cc/paper_files/paper/2020/hash/1457c0d6bfcb4967418bfb8ac142f64a-Abstract.html)

- Status: recommended
- Role: scaling and in-context learning advance
- Lane: LLM
- Subarea: zero-shot, one-shot, and few-shot in-context learning
- Core: GPT-3 scales an autoregressive GPT-2-style model to 175B parameters and specifies tasks plus demonstrations only in text, with no gradient update at evaluation time.
- Why today: It turns GPT-2's emerging zero-shot behavior into a systematic study of task-agnostic in-context adaptation and is the next prerequisite after the selected pair.
- Relation: It explicitly uses GPT-2's model and architecture with limited attention-pattern changes; it is the base model that InstructGPT later post-trains with demonstrations and preferences.
- Handoff: Legal full text: https://proceedings.neurips.cc/paper_files/paper/2020/file/1457c0d6bfcb4967418bfb8ac142f64a-Paper.pdf ; reconsider next because it is deferred only by the two-note limit.

### 4. [Training language models to follow instructions with human feedback](https://proceedings.neurips.cc/paper_files/paper/2022/hash/b1efde53be364a73914f58805a001731-Abstract.html)

- Status: recommended
- Role: general instruction-alignment advance
- Lane: LLM
- Subarea: supervised instruction tuning and reinforcement learning from human feedback
- Core: InstructGPT post-trains GPT-3 with labeler demonstrations, a preference reward model, and PPO, while PPO-ptx mixes pre-training updates back in to reduce capability regressions.
- Why today: It explains why next-token scaling and in-context prompting do not by themselves make a model reliably follow user intent, completing the batch's training-objective evolution.
- Relation: It directly starts from GPT-3 and cites WebGPT; compared with WebGPT, it generalizes human-feedback post-training from browser-assisted question answering to a broad API instruction distribution.
- Handoff: Legal full text: https://proceedings.neurips.cc/paper_files/paper/2022/file/b1efde53be364a73914f58805a001731-Paper-Conference.pdf ; reconsider after GPT-3, deferred only by the two-note limit.

### 5. [WebGPT: Browser-assisted question-answering with human feedback](https://openai.com/index/webgpt/)

- Status: recommended
- Role: tool-conditioned human-feedback bridge
- Lane: LLM
- Subarea: browser-assisted question answering and preference-based post-training
- Core: WebGPT fine-tunes GPT-3 to browse and cite sources, first by behavior cloning and then with a human-preference reward model used for rejection sampling or reinforcement learning.
- Why today: It makes the transition from GPT-3's prompt-only elicitation to feedback-trained behavior concrete before InstructGPT applies the pattern across general instructions.
- Relation: It directly derives from GPT-3 and provides a task-specific human-feedback predecessor cited by InstructGPT; the WebGPT-to-InstructGPT relation is a source-backed analytical progression rather than a claim of strict model lineage.
- Handoff: Legal full text: https://cdn.openai.com/WebGPT.pdf ; reconsider in a post-training or tool-use batch, with no reserved slot.

## Future Handoff

- 本批的连通学习图是 GPT-1 的生成式预训练与监督微调 → GPT-2 的规模化与零样本迁移 → GPT-3 的系统化上下文学习；GPT-3 再通向 WebGPT 的工具化偏好训练与 InstructGPT 的通用指令对齐，WebGPT 被 InstructGPT 直接引用。
- GPT-1 与 GPT-2 已通过完整官方 PDF 来源门槛并完成摄入；选择 GPT-2 而非直接跳到 GPT-3，是因为它是最近的直接谱系前置，并让两篇摄入形成最紧密的 foundation-to-bridge 单元。
- GPT-3、InstructGPT 与 WebGPT 均通过推荐门槛，仅因两篇自动笔记上限和阅读前置而未摄入，不是质量、来源、可信度或连通性淘汰，也没有未来保留名额。
- GPT-4 Technical Report 是最强近失候选：它是编号谱系终点并披露 next-token pre-training、RLHF 与可预测扩展，但明确不公开架构、模型规模、训练算力、数据构造及具体训练方法，因此对本次“训练范式如何演进”的可复核学习价值低于 WebGPT。
- T5 与 ViT 虽通过上一轮推荐门槛，但不属于 GPT 直接谱系；Sarathi-Serve、DistServe 与 Llumnix 仍是 AI Infra 后续强候选；Agent taxonomy 时效性复核仍待开展。本批不混合这些方向。
- 影响力元数据 caveat：OpenAlex 没有为 GPT-1 与 GPT-2 返回可信的精确记录，并把 GPT-4 的预印本错误关联到无关 DOI；后续不得据此补写引用数或 DOI。

---
type: paper-intake-history
status: completed
topic: "经典 Transformer 架构及其代表性扩展"
research_lanes: ["LLM"]
intake_run_at: "2026-08-16T17:34:59+08:00"
created: 2026-08-16
updated: 2026-08-16
---

# Paper Intake: 经典 Transformer 架构及其代表性扩展

## Direction Decision

本次临时引导以最高优先级明确要求先学习经典 Transformer 架构，并在来源与质量门槛满足时优先摄入 Vaswani et al. (2017) 的 *Attention Is All You Need*；这覆盖了稳定配置之外一个明确的短期学习任务。当前仓库已有 Agent 综述与 ReAct，以及 AI Infra 的 JENGA、Orca 和 vLLM/PagedAttention，但没有以模型架构贡献为对象的 Transformer 规范笔记；Orca 与 vLLM 中出现 Transformer 只是推理系统的工作负载背景，不构成 scaled dot-product attention、multi-head attention、位置编码和 encoder-decoder 设计的架构覆盖。最近仅有 2 次可识别 intake，仍处于 warm-up，历史平衡只提供弱背景，真正驱动本次选题的是最高优先级临时引导和这个清晰的知识缺口。

本批次围绕“Transformer 怎样用注意力与位置表示替代序列递归，并让同一组核心部件演化为不同的预训练与跨模态架构”组织。学习路径先用原始论文建立注意力计算、multi-head 分解、位置编码、encoder-decoder 堆叠、残差连接与归一化的共同基线，再用互补的经典角色观察 encoder-only 表征学习、decoder-only 生成、统一 encoder-decoder 迁移和向视觉等新模态的扩展；这种角色组合能展示哪些设计被保留、裁剪或重新解释，而不是把“广泛采用”简化为影响力名单。上一轮的 Sarathi-Serve、DistServe、Llumnix，以及更早的 Toolformer、Reflexion，都是仍可重新竞争的强 carry-over，但均与本批架构问题不连通，因此只作为延后方向，不占本次候选名额。

AI Infra 的 prefill/decode 协同与跨实例编排是最强的备选方向，因为它已有三个经核验但因上限未摄入的候选；Agent 架构划分的时效性复核也是明确的后续任务。本次暂缓二者，是因为配置把 Transformer 指定为“下一次 intake”的最高优先级，并明确禁止把三个方向混成同一批次，而不是因为这些方向质量不足或已被覆盖。

## Learning Goal And Reading Path

- 学习问题：读完本批后，应能从张量计算与信息流解释原始 Transformer 的核心部件，并比较后续经典架构为何保留、裁剪或迁移这些部件。
- 阅读路径：原始 encoder-decoder 架构与实验论证 → encoder-only 表征学习与 decoder-only 生成两条分化路线 → 统一 text-to-text encoder-decoder 迁移 → 跨模态扩展，用直接引用或可核验的架构继承关系连接各角色。
- 历史窗口：检查了最近 2 次可识别 intake；少于 7 次，属于 warm-up，历史与 carry-over 仅作弱背景和延后方向依据。

## Paper Decisions

### 1. [Attention Is All You Need](https://papers.nips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html)

- Status: ingested
- Role: foundational architecture
- Lane: LLM
- Subarea: Transformer architecture and sequence transduction
- Core: The paper replaces sequence-aligned recurrence and convolution with an encoder-decoder built from scaled dot-product multi-head attention, position-wise feed-forward layers, positional encodings, residual connections, and layer normalization.
- Why today: It is the explicitly prioritized prerequisite and the only candidate that directly establishes every core mechanism named in the temporary guidance together with the original translation evidence.
- Relation: It is the verified architectural parent directly cited and adapted by GPT, BERT, T5, and ViT, so it anchors the complete connected batch.
- Handoff: Ingested as [[paper-vault/papers/vaswani2017attention]] from the verified NeurIPS proceedings PDF; the exact inspected artifact was newly copied to the configured external PDF directory after the note was created.

### 2. [BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding](https://aclanthology.org/N19-1423/)

- Status: ingested
- Role: representative encoder-only adoption
- Lane: LLM
- Subarea: bidirectional language representation pre-training
- Core: BERT keeps an almost unchanged Transformer encoder, pre-trains all layers bidirectionally with masked language modeling and next-sentence prediction, then fine-tunes the same parameters with small task heads across eleven NLP tasks.
- Why today: It gives the clearest peer-reviewed demonstration that the original encoder can become a broadly reusable representation learner while also providing a controlled contrast with causal GPT.
- Relation: BERT directly cites the original Transformer and explicitly identifies its encoder as the architectural base; it also compares against GPT with nearly matched model size and isolates bidirectionality in ablations.
- Handoff: Ingested as [[paper-vault/papers/devlin2019bert]] from the verified ACL Anthology PDF; the exact inspected artifact was newly copied to the configured external PDF directory after the note was created.

### 3. [Improving Language Understanding by Generative Pre-Training](https://openai.com/index/language-unsupervised/)

- Status: recommended
- Role: foundational decoder-only adoption
- Lane: LLM
- Subarea: causal language-model pre-training and fine-tuning
- Core: GPT adapts a Transformer decoder into a causal language model, performs generative pre-training on BooksCorpus, and fine-tunes it for diverse language-understanding tasks with task-aware input transformations and minimal architectural changes.
- Why today: It records the key move from the original encoder-decoder model to the causal decoder-only pre-training lineage and creates the intended architectural contrast with BERT.
- Relation: The report directly cites and modifies Vaswani et al.; BERT uses it as the matched unidirectional contrast, and T5 later compares decoder-only transfer against encoder-decoder alternatives.
- Handoff: Reconsider after the two selected notes; legal full text: https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf ; deferred only by the two-note limit, with its non-peer-reviewed technical-report status kept as a caveat.

### 4. [Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer](https://jmlr.org/papers/v21/20-074.html)

- Status: recommended
- Role: systematic encoder-decoder transfer bridge
- Lane: LLM
- Subarea: text-to-text transfer learning
- Core: T5 casts every NLP task as text-to-text and systematically compares architectures, corruption objectives, data, transfer methods, and scale before combining an encoder-decoder Transformer, span corruption, and C4.
- Why today: It shows why the original two-stack form remained useful after encoder-only and decoder-only branches emerged, and replaces anecdotal adoption claims with a broad controlled study.
- Relation: T5 directly cites Vaswani and largely retains its encoder-decoder structure while comparing decoder-only, prefix-LM, and encoder-decoder variants grounded in the GPT and BERT-era transfer literature.
- Handoff: Reconsider in a transfer-learning or pre-training-objective batch; legal full text: https://www.jmlr.org/papers/volume21/20-074/20-074.pdf ; deferred only by the two-note limit.

### 5. [An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale](https://openreview.net/forum?id=YicbFdNTTy)

- Status: recommended
- Role: cross-modal bridge
- Lane: LLM
- Subarea: vision Transformer architecture
- Core: ViT turns fixed-size image patches into a token sequence and applies an almost-standard Transformer encoder with a BERT-like class token and learned positional embeddings, relying on large-scale pre-training for transfer to image classification.
- Why today: It supplies the strongest classic example that the Transformer core could leave language and displace a domain-specific convolutional prior when data scale was sufficient.
- Relation: ViT directly cites and closely follows Vaswani, and directly borrows BERT's class token and Base/Large configurations; its relation to T5 is an analytical comparison of transfer across task and modality boundaries.
- Handoff: Reconsider in a cross-modal Transformer or data-scale/inductive-bias batch; legal full text: https://openreview.net/pdf?id=YicbFdNTTy ; deferred only by the two-note limit.

## Future Handoff

- 本批五篇形成一个连通图：原始 Transformer 是共同架构根；GPT 与 BERT 分别展示 causal decoder-only 与 bidirectional encoder-only；T5 重新系统检验 encoder-decoder 迁移；ViT 把 encoder 迁移到视觉 patch 序列。关系以论文中的直接引用和架构说明为主，T5 与 ViT 之间仅作有证据支持的分析性比较。
- GPT、T5 与 ViT 均已通过推荐门槛，只因本轮两篇自动笔记上限而保留推荐，不是质量、来源、可信度或连通性淘汰；后续仍需与新候选公平竞争，不预留固定名额。
- Transformer-XL 是本轮最强近失候选：它直接扩展位置表示和固定上下文，但与当前语言架构分支的角色重叠高于 ViT；后续长上下文专题可重新核验。
- 本次已新增原始 Transformer 的完整架构基线与 BERT 的 encoder-only 双向预训练覆盖；仍待深入的问题包括 causal decoder-only 的规模化路线、位置表示与长上下文扩展、以及跨模态迁移所需的数据规模与归纳偏置。
- Sarathi-Serve、DistServe、Llumnix、Toolformer 与 Reflexion 均因本批主题不匹配而延后，不是排除，也没有预留后续名额。

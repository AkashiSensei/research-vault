# LLM

## Scope And Boundary

This area is defined by the **subject of the research contribution**, not by the mere presence of an LLM in the workload. It indexes work whose primary contribution concerns LLM architectures, learning or adaptation algorithms, capabilities and behavior, reasoning and agent methods, evaluation, or ways of organizing and using LLMs.

Include a paper here when answering its main research question requires understanding or changing the model, its behavior, or how people and systems organize its use. Do not include a paper merely because its experiments train, fine-tune, or serve an LLM. If the main contribution is a kernel, compiler, memory manager, distributed runtime, scheduler, serving engine, or cluster control plane, classify it under AI Infrastructure and Systems; retain `llm` as a tag and use LLM-specific topics and scenarios for workload-level discovery.

This boundary is especially important now that LLMs are the dominant workload in much of AI Infrastructure research. If every paper that merely involved an LLM were added here, most contemporary work on training systems, inference serving, operator optimization, and AI platforms would also enter the LLM area. The area would then collapse into a broad workload label, duplicate much of AI Infrastructure, and stop distinguishing research on LLMs from research on the systems that run them.

Use the following decision rule:

- **Model, behavior, or use is the object of study:** include the paper in LLM. ReAct and research on autonomous-agent organization are examples.
- **Compute, memory, communication, scheduling, or platform operation is the object of study:** do not include the paper in LLM solely because the workload is an LLM. Orca, vLLM/PagedAttention, and JENGA are examples.
- **Both are substantive research contributions:** cross-list only when the paper makes independent contributions on both sides, not when the LLM merely supplies the benchmark or workload.

## 2026-07

- [[paper-vault/papers/wang2024autonomousagents]]
  - Title: A survey on large language model based autonomous agents
  - Subarea: LLM agents; architecture, capability acquisition, applications, and evaluation.
  - Why listed here: Studies how LLMs function as persistent agent controllers and organizes the surrounding profile, memory, planning, action, and evaluation mechanisms.
- [[paper-vault/papers/yao2023react]]
  - Title: ReAct: Synergizing Reasoning and Acting in Language Models
  - Subarea: LLM reasoning, tool use, and interactive agents.
  - Why listed here: Expands an LLM policy with natural-language thoughts and environment actions so external observations can ground and revise later reasoning.

## 2026-08

- [[paper-vault/papers/vaswani2017attention]]
  - Title: Attention Is All You Need
  - Subarea: Foundational Transformer architecture for language modeling and sequence transduction.
  - Why listed here: Replaces recurrent and convolutional sequence processing with attention-based encoder-decoder blocks, establishing the architecture later scaled into modern LLMs.
- [[paper-vault/papers/devlin2019bert]]
  - Title: BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding
  - Subarea: Bidirectional language-model pretraining and transfer to language-understanding tasks.
  - Why listed here: Pretrains one Transformer encoder with masked-token and sentence-relation objectives, then adapts it across token- and sentence-level tasks with minimal task-specific structure.
- [[paper-vault/papers/radford2018improving]]
  - Title: Improving Language Understanding by Generative Pre-Training
  - Subarea: Causal decoder-only pre-training and supervised task transfer.
  - Why listed here: Establishes the original GPT pipeline: learn a reusable causal Transformer from unlabeled text, serialize heterogeneous task inputs as token sequences, and fine-tune the full model with minimal task-specific structure.
- [[paper-vault/papers/radford2019language]]
  - Title: Language Models are Unsupervised Multitask Learners
  - Subarea: Web-scale causal language modeling and zero-shot task transfer.
  - Why listed here: Scales the GPT architecture, objective, and training corpus, then treats downstream tasks as conditional language modeling without task-specific parameter updates.

## 2026-09

- [[paper-vault/papers/yehudai2026evaluation]]
  - Title: A Survey on Evaluation of LLM-based Agents
  - Subarea: Evaluation of LLM backbones embedded in interactive agent harnesses.
  - Why listed here: Shows why model-only response metrics cannot explain planning, tool use, memory, trajectory, cost, and safety behavior once an LLM controls an agent.
- [[paper-vault/papers/mohammadi2025evaluation]]
  - Title: Evaluation and Benchmarking of LLM Agents: A Survey
  - Subarea: Operational evaluation of LLM-agent behavior, capabilities, reliability, and alignment.
  - Why listed here: Organizes evidence about LLM-based agents across objective and process axes, including judge methods, repeated runs, and online monitoring.
- [[paper-vault/papers/zhu2027evolutionary]]
  - Title: Evolutionary perspectives on the evaluation of LLM-based AI agents: a comprehensive survey
  - Subarea: Evolution from LLM chatbots to environmentally situated agents.
  - Why listed here: Identifies which changes beyond text generation require new benchmarks and relates external environments to planning, reflection, interaction, and memory capabilities.

---
citekey: wang2024autonomousagents
title: "A survey on large language model based autonomous agents"
authors:
  - Lei Wang
  - Chen Ma
  - Xueyang Feng
  - Zeyu Zhang
  - Hao Yang
  - Jingsen Zhang
  - Zhiyuan Chen
  - Jiakai Tang
  - Xu Chen
  - Yankai Lin
  - Wayne Xin Zhao
  - Zhewei Wei
  - Jirong Wen
year: 2024
venue: "Frontiers of Computer Science"
type: paper
status: ingested
tags:
  - large-language-models
  - autonomous-agents
  - survey
  - agent-architecture
  - agent-evaluation
topics:
  - Agent Architecture
  - Agent Evaluation
areas:
  - LLM
  - Agents
scenarios:
  - Agent System Design
  - Agent Evaluation
methods:
  - Literature Survey
  - Agent Taxonomy
doi: "10.1007/s11704-024-40231-1"
arxiv: "2308.11432"
official_url: "https://link.springer.com/article/10.1007/s11704-024-40231-1"
pdf_url: "https://link.springer.com/content/pdf/10.1007/s11704-024-40231-1.pdf"
code_url: "https://github.com/Paitesanshi/LLM-Agent-Survey"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 26-page open-access Springer version of record was inspected, including tables, references, and author information."
  - "Title, author order, venue, volume and issue, article number, DOI, and publication year were cross-checked against the PDF first page and the official Springer record."
  - "The companion repository is maintained by the paper authors and tracks related literature; it is not a software implementation."
  - "The review is treated as a curated historical synthesis, because it does not report a reproducible literature-search and screening protocol."
created: 2026-07-29
updated: 2026-08-05
---

# A survey on large language model based autonomous agents

## Summary

> **User-directed revision:** Rewritten as a flexible pre-reading overview and expanded with a five-point interpretation of the cited agent definition.

This paper is best read as a **map of early LLM-agent research**, rather than as a proposal for one new agent. It begins from Franklin and Graesser's (1997) definition:

> “An autonomous agent is a system situated within and a part of an environment that senses that environment and acts on it, over time, in pursuit of its own agenda and so as to effect what it senses in the future.”

The definition can be unpacked into five properties:

- **Situated:** An agent exists within—and as part of—an environment.
- **Perceptive:** It can sense the environment in which it is situated.
- **Persistent:** It acts over time rather than merely producing one isolated response.
- **Goal-directed:** Its behavior is organized around its own goal or agenda.
- **Environment-shaping:** Its actions affect the environment and thereby influence what it will sense in the future.

These five properties set the baseline for the survey. By organizing roughly 100 works, the paper then asks three connected questions:

1. **How is an LLM-based agent constructed?** The survey describes an agent around four modules: profiling establishes who the agent is, memory preserves what it has experienced, planning decides what to do next, and action turns that decision into an external or internal effect.
2. **How does the agent acquire capabilities?** Abilities may be learned through fine-tuning, supplied in context through prompting, or created by mechanisms around the model such as feedback, reflection, collaboration, and experience accumulation.
3. **Where are agents used and how are they evaluated?** The paper maps applications across social science, natural science, and engineering, then separates subjective human evaluation from objective task, behavior, and efficiency measures.

The architectural throughline is a feedback loop:

```text
Profile
  ↓
Memory ↔ Planning
  ↓
Action → Environment
          └─ feedback → Memory / Planning
```

The paper's core logic is that **architecture provides the structure for persistent behavior, while capability acquisition determines what that structure can accomplish**. Applications expose the behavior in concrete settings, and evaluation asks whether the resulting agent is effective, efficient, and plausibly aligned with human expectations.

Its lasting value is therefore a shared vocabulary and reading order: an LLM agent is not merely a model with tool calls, but an LLM controller combined with identity, accumulated state, future-directed reasoning, an action interface, and feedback. Its main limitation is temporal and methodological—the map largely reflects the 2021–August 2023 wave of work and is a curated narrative synthesis rather than a reproducible systematic review.

## Problem

Early LLM-agent systems were often presented independently, with different terminology for roles, memory, reasoning, tools, feedback, and evaluation. That fragmentation makes it difficult to see which components are common, how an agent obtains new abilities, which application domains are being studied, and how results across subjective studies and interactive benchmarks relate. The review asks two central construction questions—how to design the agent around an LLM and how to give that agent task capabilities—then extends the map to applications and evaluation.

## Research Area And Scenario

- Area(s): LLM and Agents.
- Scenario(s): Designing LLM-centered agent systems and evaluating their behavior across interactive tasks, simulations, tools, software environments, and embodied settings.
- Why it matters here: The paper supplies an entry-point taxonomy for a vault that previously contained no canonical Agent paper. It links architecture, capability acquisition, application, and evaluation rather than treating tool use or planning as isolated prompt patterns.
- Indexing rationale: It belongs in LLM because the language model is the central controller, in Agents because the subject is situated autonomous behavior, in Agent System Design because it decomposes the controller into modules, and in Agent Evaluation because it compares subjective and objective protocols.

## Assumptions And Scope

- Stated assumptions:
  - Claim: The organizing object is an autonomous agent with an LLM as its central controller.
    - Key constraint: **LLM-centered control**.
    - Why it matters: Profile prompts, natural-language memory, reasoning and planning, and tool calls all depend on the model's pretrained knowledge and language interface.
    - Excludes or weakens: Classical autonomous agents without an LLM core and most reinforcement-learning work that is not directly connected to language agents.
  - Claim: Profiling, memory, planning, and action form a unifying architecture, not a mandatory implementation.
    - Key constraint: **descriptive abstraction**.
    - Why it matters: Real systems can omit, merge, or distribute modules; the paper's mapping table explicitly includes missing or unreported components.
    - Excludes or weakens: The claim that every successful agent has four cleanly separable software modules.
  - Claim: The mapped literature represents the first rapid wave of LLM agents.
    - Key constraint: **January 2021–August 2023** for the publication-growth view, with a mainly 2022–2023 corpus.
    - Why it matters: The paper is strongest as a historical field map.
    - Excludes or weakens: Treating it as a complete account of later multimodal agents, computer-use agents, safety methods, or post-2023 evaluation.
- Implied assumptions:
  - Claim: The authors' curated corpus is broad enough to support the proposed categories.
    - Key constraint: **unreported search coverage**.
    - Why it matters: The paper states that it compiled 100 relevant works but does not disclose databases, queries, screening criteria, deduplication, or quality assessment.
    - Excludes or weakens: Coverage estimates, publication-bias analysis, and claims of systematic-review-level completeness.
- What is ignored or abstracted away:
  - Claim: Heterogeneous results from cited systems are summarized without a controlled cross-paper comparison.
    - Key constraint: **non-comparable evidence** across models, prompts, simulators, tasks, metrics, and human studies.
    - Why it matters: The taxonomy describes what has been tried; it does not identify causal effects of individual modules or produce meta-analytic effect sizes.
    - Excludes or weakens: Ranking planning, memory, or action mechanisms by performance using this review alone.
- Applies when:
  - Claim: A reader needs a compact conceptual map of early LLM-agent architecture, capability acquisition, applications, and evaluation.
    - Key constraint: **orientation rather than current frontier coverage**.
    - Why it matters: The categories create a useful reading order and shared terminology for following primary papers.
- May not apply when:
  - Claim: The task requires current best practices, a reproducible systematic review, or evidence that one architecture is superior.
    - Key constraint: **descriptive historical synthesis**.
    - Why it matters: Later systems and controlled comparative evidence fall outside the review's effective horizon and method.

## Core Idea

The review treats agent architecture as the structure that lets an LLM behave over time and capability acquisition as the process that teaches or elicits useful behavior. Its architectural dependency is profile → memory/planning → action, with environment feedback returning through memory and planning. It then places those systems in three application families—social science, natural science, and engineering—and separates subjective human evaluation from objective task, similarity, and efficiency measures.

## Method

> **User-directed revision:** Reorganized by moving the four architecture modules into a dedicated section below.

This is a taxonomy-building review rather than a runnable model or system. The authors collect representative early LLM-agent studies, compare their reported designs, and synthesize four outputs: a modular architecture, a capability-acquisition taxonomy, an application map, and an evaluation taxonomy. Tables then map representative papers into those categories, while the final discussion derives six open challenges.

The construction view has two layers. The architecture layer describes what an agent contains and how profile, memory, planning, action, and feedback form a persistent control loop. The capability layer describes how the agent gains task-specific abilities. Both are detailed in the following sections.

- Component: Evaluation taxonomy
  - Role: Organize evidence about whether an agent behaves effectively and plausibly.
  - Input: Agent trajectories, human judgments, task outcomes, behavioral similarity measures, and efficiency measurements.
  - Output: Subjective ratings or Turing-style judgments plus objective task, similarity, and efficiency scores.
  - Transformation: Evaluates agents through simulations, social settings, multi-task environments, software tests, and dedicated benchmarks.
  - Minimal example: AgentBench places models in multiple interactive environments and scores whether they can reason, decide, and complete tasks.

## Agent Architecture Modules

> **User-directed revision:** Added and condensed the four module explanations, including clarifications on profiling, memory, planning, action, and the independence of feedback from path multiplicity.

The framework is a functional abstraction rather than a requirement that every system contain four cleanly separated services. Profile establishes identity, memory carries information across time, planning constructs possible futures, and action grounds a selected decision in an environment. Action outcomes then return as feedback to memory and planning.

### Profiling Module

Profiling specifies **who the agent is expected to be**. It means role or persona construction, not runtime or GPU performance profiling. A profile may contain demographic information, personality traits, and social relationships or responsibilities, and is usually placed in the prompt to condition later behavior.

- Handcrafting: manually defines roles and attributes, offering control but scaling poorly.
- LLM generation: expands rules or seed profiles into a larger population, improving scalability at the cost of precise control.
- Dataset alignment: derives profiles from real-world population records, preserving observed characteristics but inheriting dataset limitations.

The strategies can be combined. The survey describes how profiles are produced and used, but does not establish that simply declaring an agent to be an expert improves task accuracy.

### Memory Module

Memory preserves experience and retrieves useful information for later decisions. The paper classifies it along three dimensions:

- Structure: unified memory keeps short-term information in the current context; hybrid memory adds persistent long-term storage. Hybrid memory includes a short-term component rather than replacing it.
- Format: natural language, embeddings, databases, and structured lists can be used separately or together.
- Operation: reading selects memories by recency, relevance, and importance; writing handles storage, duplication, and overflow; reflection turns episodes into higher-level insights.

Actions produce experiences, while the memory module normally decides what and how to retain. An explicit `memory.write` can be viewed as an internal action, but storage and retrieval remain memory responsibilities.

### Planning Module

Planning converts a goal and the agent's current information into future steps. The paper first separates planning without feedback from planning that can revise itself after feedback.

- Without feedback:
  - Single-path reasoning produces a linear sequence, as in CoT, Zero-shot CoT, ReWOO, and HuggingGPT.
  - Multi-path reasoning considers alternatives. CoT-SC samples complete reasoning paths and votes on comparable final answers; ToT incrementally searches a tree; LMZSP generates candidate next steps and aligns them with admissible actions; RAP combines an LLM world model with MCTS.
  - External planners let the LLM translate a task into a formal representation such as PDDL, then delegate search or low-level control to another planner, as in LLM+P, LLM-DP, and CO-LLM.
- With feedback:
  - Environmental feedback supplies observations and execution results.
  - Human feedback supplies corrections or preferences.
  - Model feedback uses an LLM or evaluator as a critic.

Feedback and path multiplicity are separate dimensions: multi-path determines how many candidate futures are considered, while feedback determines whether new observations or evaluations can revise later planning. A planner may therefore be single- or multi-path, with or without feedback.

The paper uses “reasoning step,” “thought,” and “plan step” broadly. These are normally candidate planning units rather than actions already executed in the real environment; only a selected action is passed downstream.

### Action Module

Action turns a selected decision into a concrete effect, such as an utterance, tool call, executable command, model invocation, or embodied operation. The paper classifies action from four perspectives:

- Goal: task completion, communication, or environment exploration.
- Production: recall a relevant action from memory or follow a pre-generated plan; the two can be combined.
- Space: use external APIs, databases, knowledge bases, or specialist models, or rely on the LLM's own conversation and commonsense capabilities.
- Impact: change the external environment, alter the agent's internal state, or trigger later actions.

Planning may consider many candidates, but Action normally executes only the selected one. The resulting observation then updates memory or triggers replanning.

## Agent Capability Acquisition

> **User-directed revision:** Added a concise standalone summary of the paper's capability-acquisition taxonomy.

Capability acquisition is not a fifth architecture module. It describes how task-specific knowledge, skills, and experience enter the agent. The paper formally divides methods by whether they update the LLM parameters:

- With fine-tuning: Human-annotated, LLM-generated, or real-world datasets are used to encode capabilities in model weights. The result is relatively persistent but requires access to the model parameters and training resources.
- Without fine-tuning:
  - Prompt engineering places instructions, examples, knowledge, or reflections in the current context. It usually elicits existing LLM capabilities and is limited by the context window.
  - Mechanism engineering improves the surrounding agent system through:
    - Trial-and-error: revise behavior from critic or environment feedback.
    - Crowd-sourcing: combine and reconcile outputs from multiple agents.
    - Experience accumulation: store successful actions, feedback, or reusable skills in memory.
    - Self-driven evolution: let agents set goals, explore, and improve through continued interaction.

These strategies can be combined. Their main difference is where the capability resides: model weights, the current prompt, or the external agent mechanism.

## Agent Application Domains

> **User-directed revision:** Added a concise standalone summary of Chapter 3's application domains and uses.

Chapter 3 groups agent applications by domain rather than architecture:

- Social science: psychology and mental-health support, political and economic analysis, social simulation, law, and research assistance.
- Natural science: documentation and data management, experimental assistance, and science or mathematics education.
- Engineering: civil and software engineering, industrial automation, and robotics or embodied AI.

Across these domains, agents are mainly used to simulate human behavior, assist knowledge work, coordinate tools and workflows, or act in digital and physical environments.

## Agent Evaluation

> **User-directed revision:** Added a standalone summary of subjective and objective evaluation and clarified metrics, protocols, and benchmarks.

Chapter 4 separates subjective and objective evaluation:

- Subjective evaluation: human annotation scores or ranks agent quality, while a Turing test asks whether agent behavior can be distinguished from human behavior. Human likeness alone does not imply correctness, usefulness, or safety.
- Objective evaluation:
  - Metrics define the quantities measured, mainly task success, human similarity, and efficiency.
  - Protocols define how the evaluation is conducted. The survey uses this term broadly for environment simulation, social evaluation, multi-task evaluation, and software testing, mixing environment, interaction, and task settings.
  - Benchmarks provide concrete, reusable task suites, environments, interfaces, and scoring setups.

In short, metrics specify what is measured, protocols specify how the test proceeds, and benchmarks specify the concrete testbed. Subjective and objective evidence are complementary and are best combined.

## Agent Challenges

> **User-directed revision:** Added a standalone six-item summary of the challenges identified in Chapter 6.

Chapter 6 highlights six unresolved challenges:

- Role-playing capability: uncommon or psychologically complex roles are difficult to model accurately and consistently.
- Generalized human alignment: agents must reconcile safe behavior with application-specific simulation of diverse human values.
- Prompt robustness: multi-module prompt frameworks are sensitive to small changes and may not transfer reliably across LLMs.
- Hallucination: confidently generated errors can become dangerous when converted into code, decisions, or actions.
- Knowledge boundary: an agent may use information that the person or role it simulates should not possess.
- Efficiency: repeated LLM calls for memory, planning, and action amplify inference latency and cost.

## Experiments And Evidence

- The paper introduces no new agent, benchmark, ablation, or user study; its evidence consists of taxonomic synthesis and mappings of cited work.
- Figure 1 charts cumulative paper growth from January 2021 through August 2023 using the authors' curated categories.
- Table 1 maps 31 representative systems to profile, memory, planning, action, and capability-acquisition categories.
- Table 2 maps representative agent systems to social-science, natural-science, and engineering applications.
- Table 3 maps 30 representative studies to subjective and objective evaluation protocols and benchmark use.
- Section 5 states that the authors compiled 100 relevant works. Because the paper does not expose a formal selection pipeline, these counts should be read as evidence of breadth within the authors' corpus, not a reproducible estimate of the field.

## Contributions

- Proposes a profile–memory–planning–action vocabulary that makes heterogeneous early agent systems easier to compare.
- Separates capability acquisition through fine-tuning, prompt engineering, and mechanism engineering.
- Organizes applications across social science, natural science, and engineering.
- Organizes evaluation into subjective and objective evidence, including task success, human similarity, efficiency, and interactive protocols.
- Identifies open problems in role-playing, generalized human alignment, prompt robustness, hallucination, knowledge boundaries, and efficiency.

## Limitations

- The review does not disclose a reproducible search, screening, deduplication, or study-quality procedure despite describing the work as systematic.
- Its effective literature horizon is mainly August 2023, so rapidly developing later architectures and evaluation methods are absent.
- Categories overlap and can be coarse; the authors intentionally include some highly related studies that do not call themselves LLM-agent papers.
- The unified architecture is descriptive and is not validated through controlled component ablations.
- Reported evidence comes from heterogeneous source papers and cannot support cross-system performance rankings or causal conclusions.
- The human-memory and human-planning analogy is a design heuristic rather than evidence of cognitive fidelity.
- Some bibliography text and individual-work paraphrases require cross-checking against primary sources; for example, Toolformer is better understood from its own paper than from the review's brief description.

## Key Takeaways

- A useful first approximation of an LLM agent is an LLM controller plus identity, state and experience, future-directed reasoning, and an action interface.
- Memory and planning form a feedback loop: actions produce observations, observations update memory or state, and that evidence changes later plans.
- Agent capability can live in model weights, in the current context, or in the surrounding mechanism; each location has different data, compute, context, and engineering costs.
- Agent evaluation is multi-dimensional and should combine task outcomes, efficiency, behavioral evidence, and human judgment when appropriate.
- Use the paper to orient primary-source reading, not as a current or quantitatively exhaustive account of the field.

## Relation: Previous Work

- Title: ReAct: Synergizing Reasoning and Acting in Language Models
  Authors: Shunyu Yao; Jeffrey Zhao; Dian Yu; Nan Du; Izhak Shafran; Karthik Narasimhan; Yuan Cao.
  URL: https://arxiv.org/abs/2210.03629
  Obsidian: [[yao2023react]]
  - Type: system
  - Status: ingested
  - Work summary: ReAct interleaves language reasoning traces with task actions so observations from external environments update subsequent reasoning.
  - Role: Principal example of environmental-feedback planning and tool-mediated action in the review.
  - Limitation: The review maps ReAct into its taxonomy but does not reproduce its experiments or fully analyze its prompt and benchmark dependencies.
  - This paper: Places ReAct in the unified planning, action, and evaluation map.
- Title: Toolformer: Language Models Can Teach Themselves to Use Tools
  Authors: Timo Schick; Jane Dwivedi-Yu; Roberto Dessì; Roberta Raileanu; Maria Lomeli; Eric Hambro; Luke Zettlemoyer; Nicola Cancedda; Thomas Scialom.
  URL: https://proceedings.neurips.cc/paper_files/paper/2023/hash/d842425e4bf79ba039352da0f658a906-Abstract-Conference.html
  - Type: system
  - Status: not-ingested
  - Work summary: Toolformer self-supervises a language model to decide which API to call, when to call it, which arguments to pass, and how to use returned results.
  - Role: Representative learned tool-use system in the action-space and fine-tuning taxonomy.
  - Limitation: The review gives only a brief and partly misleading prose characterization of Toolformer.
  - This paper: Uses Toolformer to represent external tool action and parameter-based capability acquisition; Toolformer's primary paper should govern technical interpretation.
- Title: Reflexion: Language Agents with Verbal Reinforcement Learning
  Authors: Noah Shinn; Federico Cassano; Ashwin Gopinath; Karthik Narasimhan; Shunyu Yao.
  URL: https://proceedings.neurips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract-Conference.html
  - Type: system
  - Status: not-ingested
  - Work summary: Reflexion turns task feedback into verbal reflection retained in episodic memory, improving later trials without weight updates.
  - Role: Cross-cutting example for natural-language memory, reflection, feedback-conditioned planning, and mechanism-based capability acquisition.
  - Limitation: The review does not evaluate when verbal reflection is reliable or how feedback quality changes its effect.
  - This paper: Uses Reflexion to connect memory, feedback, planning, and iterative improvement.
- Title: AgentBench: Evaluating LLMs as Agents
  Authors: Xiao Liu; Hao Yu; Hanchen Zhang; Yifan Xu; Xuanyu Lei; Hanyu Lai; Yu Gu; Hangliang Ding; Kaiwen Men; Kejuan Yang; Shudan Zhang; Xiang Deng; Aohan Zeng; Zhengxiao Du; Chenhui Zhang; Sheng Shen; Tianjun Zhang; Yu Su; Huan Sun; Minlie Huang; Yuxiao Dong; Jie Tang.
  URL: https://openreview.net/forum?id=zAdUB0aCTQ
  - Type: benchmark
  - Status: not-ingested
  - Work summary: AgentBench evaluates language models as interactive agents across eight heterogeneous environments.
  - Role: Representative multi-task objective benchmark in the review's evaluation taxonomy.
  - Limitation: The review catalogs AgentBench but gives no detailed critique of its coverage, adapter effects, or benchmark validity.
  - This paper: Uses AgentBench as evidence that environment-based agent evaluation was becoming systematic.

## Relation: Compared With

- Title: Augmented Language Models: a Survey
  Authors: Grégoire Mialon; Roberto Dessì; Maria Lomeli; Christoforos Nalmpantis; Ram Pasunuru; Roberta Raileanu; Baptiste Rozière; Timo Schick; Jane Dwivedi-Yu; Asli Celikyilmaz; Edouard Grave; Yann LeCun; Thomas Scialom.
  URL: https://arxiv.org/abs/2302.07842
  - Type: compares-with
  - Status: not-ingested
  - Work summary: This survey organizes language models augmented with reasoning and external-tool use.
  - Similarity: Both place reasoning, external information, and tools around an LLM core.
  - Difference: Mialon et al. center model augmentation, while this paper centers autonomous-agent architecture, capability acquisition, applications, and evaluation.
  - When to use which: Use Mialon et al. for a focused view of reasoning and tool augmentation; use this paper for a broader map of the early agent ecosystem.

## Source Notes

- Metadata sources:
  - Official Springer version-of-record page: https://link.springer.com/article/10.1007/s11704-024-40231-1
  - Official open-access Springer PDF: https://link.springer.com/content/pdf/10.1007/s11704-024-40231-1.pdf
  - arXiv record: https://arxiv.org/abs/2308.11432
  - Author-maintained companion repository: https://github.com/Paitesanshi/LLM-Agent-Survey
- Source verification: The complete official PDF was inspected, including its architecture, capability-acquisition, application, evaluation, challenge, and reference sections. Bibliographic metadata was checked against the publisher record and PDF first page; related-work identities were checked against their primary records where used.
- PDF policy: The source PDF was acquired temporarily outside the repository and was not copied into the vault. Only the stable legal source URL is retained.

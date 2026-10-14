---
citekey: zhu2027evolutionary
title: "Evolutionary perspectives on the evaluation of LLM-based AI agents: a comprehensive survey"
authors:
  - Jiachen Zhu
  - Menghui Zhu
  - Renting Rui
  - Rong Shan
  - Congmin Zheng
  - Bo Chen
  - Yunjia Xi
  - Jianghao Lin
  - Weiwen Liu
  - Ruiming Tang
  - Yong Yu
  - Weinan Zhang
year: 2027
venue: "Frontiers of Computer Science"
type: paper
status: ingested
tags:
  - llm-agents
  - agent-evaluation
  - review
  - benchmark-taxonomy
  - benchmark-selection
topics:
  - Agent Evaluation
  - Benchmark Design
areas:
  - LLM
  - Agents
scenarios:
  - Agent Evaluation
methods:
  - Literature Survey
  - Agent Evaluation Taxonomy
  - Benchmark Selection Framework
doi: "10.1007/s11704-026-51590-2"
arxiv: "2506.11102"
official_url: "https://journal.hep.com.cn/fcs/EN/10.1007/s11704-026-51590-2"
pdf_url: "https://journal.hep.com.cn/fcs/EN/PDF/10.1007/s11704-026-51590-2"
code_url: null
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 36-page open-access publisher PDF was inspected, including all benchmark tables, the discussion, appendices, and references."
  - "Title, author order, DOI, venue, volume, issue, and article number were cross-checked against the publisher page and PDF first page."
  - "The formal citation year is 2027 because the article appears in Frontiers of Computer Science volume 21, issue 1, January 2027; the record also states received September 30, 2025, accepted January 11, 2026, and copyright 2026."
  - "The publisher designates the paper as a Review Article, but the paper does not disclose search databases, queries, screening criteria, review counts, or study-quality assessment, so it is treated as a narrative taxonomy review rather than a systematic review."
  - "No companion code or survey repository was identified from the paper or verified records."
created: 2026-09-14
updated: 2026-09-14
---

# Evolutionary perspectives on the evaluation of LLM-based AI agents: a comprehensive survey

## Core Contribution

This review distinguishes LLM-based agents from chatbots, organizes agent benchmarks by external environment and internal capability, and turns the resulting map into a two-stage benchmark-selection method. Its evolutionary framing helps readers choose tests for an agent's present design while anticipating dynamic, stateful, personalized, efficient, and safety-aware evaluation needs.

## Summary

The paper begins by asking why ordinary chatbot evaluation is insufficient for agents. It identifies five changes in the transition from chatbot to agent: operation in complex environments, instructions from multiple sources, dynamic feedback, multimodal perception, and advanced internal capabilities. These differences make evaluation a question about situated interaction and development over time, not just the quality of a response to a prompt.

The main survey taxonomy selects two of those changes as organizing axes:

- **External environment:** code, web, operating system, mobile, scientific, and game environments determine the world, action space, feedback, and task constraints an agent encounters.
- **Internal capability:** planning, self-reflection, interaction with tools, humans, or other agents, memory, and general capability determine which agent competence a benchmark is intended to expose.

The paper then studies how evaluation itself is evolving through four modules. Environments move from text-only to multimodal, static to evolving, and stateless to stateful; agents move from single-agent to multi-agent and single-turn to multi-turn; evaluators move from human judges toward agent judges and from general to personalized assessment; metrics move from coarse outcomes to fine-grained feedback, effectiveness to efficiency, and accuracy toward social good.

These taxonomies culminate in a practical two-stage selection procedure. Present-focused selection locates the agent in the environment/capability map and filters candidate benchmarks by attributes such as modality, domain, data source, and metric. Future-oriented selection consults the evolutionary trends to add tests the system will need as it matures, such as continuously updated environments, stateful interactions, personalization, robustness, and safety.

## Problem

Agent benchmarks are often selected as if an LLM agent were merely a more capable chatbot, even though the agent perceives, acts, receives environmental feedback, and maintains state across a trajectory. Existing surveys can list benchmarks without explaining which properties make one appropriate for a particular agent or how that choice should change as the agent evolves. The paper seeks a conceptual boundary between chatbots and agents, a navigable taxonomy of benchmark families, and an actionable method for selecting an evaluation portfolio.

## Research Area And Scenario

- Area(s): LLM and Agents.
- Scenario(s): Selecting and evolving evaluation benchmarks for agents that operate in code repositories, websites, operating systems, mobile applications, scientific workflows, games, and multi-party interactions.
- Why it matters here: It complements objective/process taxonomies with an environment/capability map and explicitly turns survey synthesis into benchmark-selection guidance.
- Indexing rationale: It belongs in Agent Evaluation and Benchmark Design because it catalogs benchmark attributes and future evaluation directions, and in Benchmark Selection Framework because its final method links an agent's current structure and likely maturation to concrete benchmark choices.

## Assumptions And Scope

- Stated assumptions:
  - Claim: LLM chatbots and LLM-based agents differ along environment, instructor, feedback, perception, and capability.
    - Key constraint: The agent is defined by **situated, multi-step interaction**, not merely by stronger language generation.
    - Why it matters: Evaluation must expose action consequences, evolving state, multimodal observations, autonomous decisions, and feedback-driven adaptation.
    - Excludes or weakens: Static response-only tests as sufficient evidence for agents that alter external systems.
  - Claim: External environment and internal capability are the two primary axes for organizing agent benchmarks.
    - Key constraint: Multimodal perception and dynamic feedback are treated as **attributes embedded in environments**, while multi-source instruction is treated as supporting advanced capability.
    - Why it matters: The two-axis reduction makes a large benchmark landscape navigable without creating five independent taxonomies.
    - Excludes or weakens: Cases where instructor provenance, feedback structure, or modality is the dominant evaluation question and cannot be represented adequately as a secondary attribute.
  - Claim: Benchmark selection should consider both present fit and future evolution.
    - Key constraint: Future-oriented choices rely on **anticipated product and field trajectories** rather than only current requirements.
    - Why it matters: A benchmark suite chosen for a prototype may fail once the agent becomes stateful, multimodal, personalized, safety-critical, or commercially deployed.
    - Excludes or weakens: Treating the initial benchmark portfolio as a permanent certification regime.
- Implied assumptions:
  - Claim: The reviewed benchmark set is sufficiently representative to support the taxonomy and trend claims.
    - Key constraint: The corpus comes from an **undisclosed narrative-review process**.
    - Why it matters: The paper provides extensive references and tables but no reproducible search strategy, screening flow, inclusion criteria, or study-quality assessment.
    - Excludes or weakens: Exhaustiveness claims and quantitative estimates of how prevalent each benchmark property is.
  - Claim: The proposed evolutionary directions are broadly useful across domains.
    - Key constraint: The trends are **interpretive projections**, not forecasts validated longitudinally.
    - Why it matters: Some applications may remain static, single-turn, or human-evaluated for cost, safety, legal, or product reasons.
    - Excludes or weakens: Reading every arrow in Figure 3 as an inevitable or universally desirable progression.
- What is ignored or abstracted away:
  - Claim: Benchmark entries are compared by published attributes rather than by controlled reruns of the same agents.
    - Key constraint: Results are **heterogeneous across tasks, models, harnesses, interfaces, and budgets**.
    - Why it matters: The tables support benchmark discovery and structural comparison, not a causal ranking of evaluation quality or agent performance.
    - Excludes or weakens: Meta-analytic conclusions and direct score transfer across benchmarks.
- Applies when:
  - Claim: A researcher or developer needs to find benchmarks that match an agent's environment and capabilities and to plan a broader future test suite.
    - Key constraint: The selection problem is primarily about **coverage and fit**, not formal certification.
    - Why it matters: The paper's tables and two-stage procedure help turn a vague “evaluate the agent” goal into an explicit benchmark shortlist.
- May not apply when:
  - Claim: The task requires a systematic evidence review, domain-specific regulatory assurance, or empirical validation of benchmark validity.
    - Key constraint: The review is a **taxonomy and guidance artifact**, not an experimental or compliance standard.
    - Why it matters: Primary benchmark documentation, threat models, and domain rules remain necessary.

## Core Idea

The paper's intellectual move is to treat agent evaluation as co-evolving with the system being evaluated. Richer external environments demand stronger internal capabilities; those capabilities in turn require benchmarks that observe longer, more dynamic, and more consequential interaction. Environment and capability therefore locate the current evaluation problem, while environment–agent–evaluator–metric trends indicate where coverage will become inadequate next. The two-stage method realizes this logic by first matching today's agent to concrete benchmark attributes and then stress-testing that choice against the agent's likely trajectory.

## Method

The authors conduct a broad narrative review of agent benchmarks and arrange the material in three layers. First, an analytical comparison separates agents from chatbots across five aspects. Second, benchmark families are cataloged along environment and capability axes, with detailed attribute tables in the main text and Appendix A. Third, recurring changes across environment, agent, evaluator, and metric are synthesized into an evolutionary outlook and a two-stage benchmark-selection method. The paper does not document how the reviewed corpus was searched or screened.

- Component: Chatbot-to-agent analytical framework
  - Role: Define why agent evaluation needs a different unit of analysis from chatbot evaluation.
  - Input: Properties of chatbot and agent interaction across environment, instruction, feedback, perception, and capability.
  - Output: A five-aspect distinction summarized in Figure 1.
  - Transformation: Reframes the system from a reactive prompt-response model into a situated actor receiving multiple signals and producing consequential actions.
  - Minimal example: A code agent does not merely answer with code; it can execute, observe an error, revise the program, and change a repository.
- Component: Environment taxonomy
  - Role: Organize benchmarks by the external world in which actions and observations occur.
  - Input: Code, web, OS, mobile, scientific, and game benchmarks plus their modalities, tasks, data, environments, and metrics.
  - Output: Six benchmark families and comparative reference tables.
  - Transformation: Groups testbeds by interaction substrate so readers can compare realism, state, feedback, and scoring within a relevant operational domain.
  - Minimal example: A web booking agent should be matched to browser and tool-use benchmarks rather than evaluated only on static question answering.
- Component: Capability taxonomy
  - Role: Organize benchmarks by the internal competence they are designed to reveal.
  - Input: Planning, self-reflection, tool and system interaction, human-agent interaction, multi-agent interaction, memory, and general-capability evaluations.
  - Output: Capability families with attributes such as task form, data source, interaction structure, and metric.
  - Transformation: Separates the question “where does the agent act?” from “which competence is under test?” so one system can be evaluated along both axes.
  - Minimal example: A mobile environment benchmark tests operation in an application world, while a tool benchmark can isolate whether the same agent selects and calls functions correctly.
- Component: Evolutionary evaluation framework
  - Role: Identify where current benchmark coverage is likely to become insufficient.
  - Input: Patterns observed across environment, agent, evaluator, and metric design.
  - Output: Directional transitions summarized in Figure 3.
  - Transformation: Recasts benchmark history as movement toward multimodal, evolving, stateful, multi-agent, multi-turn, automated, personalized, fine-grained, efficient, and socially aware evaluation.
  - Minimal example: A final-state booking test can mature into a stateful trajectory test that also measures time, risk, and policy adherence.
- Component: Two-stage benchmark-selection methodology
  - Role: Convert the survey map into an actionable evaluation-planning procedure.
  - Input: The agent's present environment and capabilities, table attributes, expected product evolution, and future evaluation dimensions.
  - Output: A current benchmark shortlist plus a forward-looking coverage plan.
  - Transformation: First filters benchmarks for immediate construct fit, then adds or monitors benchmarks aligned with anticipated changes in environment, user context, safety, robustness, and metrics.
  - Minimal example: For a flight-and-hotel web agent, the paper selects WebVoyager and ComplexFuncBench for present coverage, then points to BFCL, ST-WebAgentBench, and PeToolBench as dynamic, safety, and personalization needs emerge.

## Experiments And Evidence

- **Chatbot versus agent evaluation → interactive agency introduces dimensions absent from response-only testing.**
  - Selected evidence: Figure 1 and Section 2 analyze complex environment, multi-source instruction, dynamic feedback, multimodal perception, and advanced capability as the transition from chatbot to agent.
  - Supports: Benchmark choice should reflect the operational loop and not only the backbone model's textual ability.
  - Original: Figure 1 and Section 2.2.
- **Environment taxonomy versus capability taxonomy → the same agent needs complementary evidence about where it acts and what it can do.**
  - Selected evidence: Sections 3 and 4 organize benchmark families into six environments and planning, self-reflection, interaction, memory, and general capabilities; Tables 1–12 and Appendix A record comparison attributes.
  - Supports: A two-axis lookup is more informative than a single undifferentiated benchmark list.
  - Original: Figure 2, Sections 3–4, Tables 1–12, and Appendix A.
- **Earlier versus emerging evaluation designs → the reviewed literature exhibits repeated shifts toward richer and more operational tests.**
  - Selected evidence: Section 5 traces environment shifts from single modality to multimodality, static to evolving, and stateless to stateful; agent shifts toward multi-agent and multi-turn; evaluator shifts toward agent judges and personalization; and metric shifts toward granularity, efficiency, and social good.
  - Supports: Evaluation portfolios should be revisited as agent autonomy and deployment complexity increase.
  - Original: Figure 3 and Sections 5.1–5.4.
- **Present-focused versus future-oriented selection → immediate construct fit and anticipated maturity answer different planning questions.**
  - Selected evidence: Section 5.5 walks through a flight-and-hotel agent, using the current environment/capability map to identify WebVoyager and ComplexFuncBench, then using trends to motivate BFCL, ST-WebAgentBench, and PeToolBench.
  - Supports: Benchmark selection can be systematic without assuming today's suite will remain sufficient.
  - Original: Section 5.5.
- Boundary note: These are taxonomic and qualitative comparisons. The paper reports no original agent runs, benchmark ablations, selection-user study, inter-rater reliability analysis, or quantitative meta-analysis.

## Contributions

- Defines five dimensions separating LLM chatbots from LLM-based agents for evaluation purposes.
- Organizes agent benchmarks along external-environment and internal-capability axes.
- Provides extensive benchmark attribute tables for code, web, OS, mobile, scientific, game, planning, tool, interaction, memory, and general evaluations.
- Synthesizes evaluation trends across environment, agent, evaluator, and metric perspectives.
- Proposes a present-focused and future-oriented benchmark-selection method with a worked web-agent example.

## Limitations

- Despite the publisher's Review Article designation and the paper's use of “systematic,” no literature-search databases, queries, dates, inclusion or exclusion rules, screening counts, deduplication method, or study-quality assessment are disclosed.
- The paper contains no explicit limitations section and does not empirically validate whether independent users apply its taxonomy or selection method consistently.
- Environment and capability categories overlap: tool use, multimodality, feedback, state, and multi-agent interaction can simultaneously describe the world, the harness, and the competence under test.
- The evolutionary arrows are useful hypotheses and design prompts, but some are normative projections rather than trends established by longitudinal measurements.
- Benchmark tables inherit stale links, changing versions, contamination risks, and validity limitations from the underlying artifacts.
- The worked selection example illustrates the procedure but does not compare it against alternative selection methods or show improved predictive validity.
- Bibliographic dating requires care: the formal volume and issue are January 2027, while acceptance and copyright information are from 2026 and the arXiv version appeared earlier.

## Key Takeaways

- Start by confirming that the evaluation tests agentic interaction rather than only chatbot response quality.
- Select benchmarks on both environment and capability axes; one axis rarely provides complete coverage.
- Inspect modality, domain, data source, state, interaction structure, and metric before treating a benchmark as a fit.
- Revisit the suite as the agent becomes multimodal, stateful, multi-turn, multi-agent, personalized, or connected to a changing environment.
- Add fine-grained trajectory feedback, efficiency, safety, robustness, and social-impact criteria alongside task effectiveness.
- Treat agent judges as a scalability direction that still requires validation, calibration, and safeguards.

## Relation: Previous Work

- Title: A survey on large language model based autonomous agents
  Authors: Lei Wang; Chen Ma; Xueyang Feng; Zeyu Zhang; Hao Yang; Jingsen Zhang; Zhiyuan Chen; Jiakai Tang; Xu Chen; Yankai Lin; Wayne Xin Zhao; Zhewei Wei; Jirong Wen.
  URL: https://doi.org/10.1007/s11704-024-40231-1
  Obsidian: [[wang2024autonomousagents]]
  - Type: survey
  - Status: ingested
  - Work summary: Wang et al. synthesize early LLM-agent architecture, capability acquisition, applications, and subjective and objective evaluation.
  - Role: Provides a broad foundation for the definition and organization of LLM-based agents used by the review.
  - Limitation: Its evaluation treatment is one section of a wider field map and predates much of the benchmark specialization covered here.
  - This paper: Centers evaluation, adds an explicit chatbot-to-agent distinction, catalogs later benchmark families, and provides selection guidance.

## Relation: Compared With

- Title: A Survey on Evaluation of LLM-based Agents
  Authors: Asaf Yehudai; Lilach Eden; Alan Li; Guy Uziel; Yilun Zhao; Roy Bar-Haim; Arman Cohan; Michal Shmueli-Scheuer.
  URL: https://doi.org/10.18653/v1/2026.findings-acl.1330
  Obsidian: [[yehudai2026evaluation]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Yehudai et al. survey capability, application-specific, generalist, benchmark-design, and framework perspectives and disclose a structured literature-review method.
  - Similarity: Both reviews emphasize dynamic environments, trajectory granularity, agent judges, efficiency, safety, and continuously updated evaluation.
  - Difference: This paper organizes benchmark discovery around environment and capability plus evolutionary selection; Yehudai et al. provide a more explicit evaluation-layer and developer-framework analysis.
  - When to use which: Use this paper for benchmark-family lookup and future-aware selection; use Yehudai et al. for a layered evaluation program, benchmark contract analysis, and framework-level observability.
- Title: Evaluation and Benchmarking of LLM Agents: A Survey
  Authors: Mahmoud Mohammadi; Yipeng Li; Jane Lo; Wendy Yip.
  URL: https://doi.org/10.1145/3711896.3736570
  Obsidian: [[mohammadi2025evaluation]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Mohammadi et al. separate evaluation objectives from evaluation processes and discuss enterprise access, reliability, long-horizon operation, and compliance.
  - Similarity: Both reviews use two organizing axes and cover capabilities, environments, interaction modes, automated judges, realistic testing, efficiency, safety, and multi-agent behavior.
  - Difference: This paper provides a larger benchmark catalog and evolution-driven selection method; Mohammadi et al. provide a more compact operational taxonomy and a stronger enterprise-governance lens.
  - When to use which: Use this paper to locate and evolve benchmark coverage; use Mohammadi et al. to audit what is measured, how evidence is produced, and what enterprise constraints remain untested.

## Source Notes

- Metadata sources:
  - Official publisher record: https://journal.hep.com.cn/fcs/EN/10.1007/s11704-026-51590-2
  - Official open-access publisher PDF: https://journal.hep.com.cn/fcs/EN/PDF/10.1007/s11704-026-51590-2
  - DOI record: https://doi.org/10.1007/s11704-026-51590-2
  - arXiv record: https://arxiv.org/abs/2506.11102
- Source verification: The complete official publisher PDF was inspected, including the chatbot-to-agent framework, environment and capability taxonomy, benchmark tables, evolutionary discussion, benchmark-selection method, appendices, and references. Metadata and the unusual 2026/2027 chronology were checked against the publisher record and first page.
- PDF policy: The source PDF was acquired temporarily outside the repository and was not copied into the vault. Only stable legal source URLs are retained.

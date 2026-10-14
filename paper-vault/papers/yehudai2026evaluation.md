---
citekey: yehudai2026evaluation
title: "A Survey on Evaluation of LLM-based Agents"
authors:
  - Asaf Yehudai
  - Lilach Eden
  - Alan Li
  - Guy Uziel
  - Yilun Zhao
  - Roy Bar-Haim
  - Arman Cohan
  - Michal Shmueli-Scheuer
year: 2026
venue: "Findings of ACL"
type: paper
status: ingested
tags:
  - llm-agents
  - agent-evaluation
  - survey
  - benchmarks
  - evaluation-frameworks
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
doi: "10.18653/v1/2026.findings-acl.1330"
arxiv: "2503.16416"
official_url: "https://aclanthology.org/2026.findings-acl.1330/"
pdf_url: "https://aclanthology.org/2026.findings-acl.1330.pdf"
code_url: "https://github.com/Asaf-Yehudai/LLM-Agent-Evaluation-Survey"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 25-page ACL Anthology version of record was inspected, including the literature-review methodology, tables, appendices, and references."
  - "Title, author order, venue, pages, DOI, and publication year were cross-checked against the official ACL Anthology record and PDF."
  - "Appendix A reports a multi-stage search, structured inclusion and exclusion criteria, citation chasing, and expert consultation, although it does not provide PRISMA-style corpus counts or a formal study-quality assessment."
  - "The companion repository is maintained by the authors and tracks works in LLM-agent evaluation; it is a survey resource rather than an implementation artifact."
created: 2026-09-14
updated: 2026-09-14
---

# A Survey on Evaluation of LLM-based Agents

## Core Contribution

This survey builds an evaluation-centered map of LLM agents that connects capability tests, domain benchmarks, generalist benchmarks, benchmark-design dimensions, and developer-facing evaluation frameworks. Its significance is that it treats an agent as a sequential system acting in an environment, so evaluation must cover trajectories, interfaces, safety, cost, and harness effects rather than only final text quality.

## Summary

The paper asks how evaluation should change when an LLM becomes the backbone of an agent that plans, invokes tools, observes consequences, and adapts over multiple steps. It answers with a five-part field map: evaluate foundational agent capabilities; evaluate agents in specific application domains; evaluate generalist agents; analyze benchmark construction; and instrument agents throughout development and deployment.

The taxonomy moves from the object being tested to the machinery used to test it:

1. **Capabilities:** planning and multi-step reasoning, function calling and tool use, self-reflection, and memory can be isolated or assessed inside a complete workflow.
2. **Applications:** web, software-engineering, scientific, and conversational agents require domain-specific environments and success criteria.
3. **Generalists:** broad suites and leaderboards test whether one agent can transfer across heterogeneous environments.
4. **Benchmarks:** data curation, environment, interaction interface, metric, and safety determine what a score actually means.
5. **Frameworks:** final-response, stepwise, and trajectory-level assessment support monitoring, debugging, human review, synthetic data, and experiment comparison.

The paper's throughline is diagnostic depth. End-to-end task success is necessary but often cannot explain why an agent failed; stepwise inspection localizes errors but may miss dependencies; trajectory assessment captures the whole route but must choose between reproducible reference paths and flexible, less reliable judge-based scoring. The authors therefore call for more granular, efficient, scalable, safety-aware evaluation and for controlled designs that separate the backbone model from the surrounding agent harness.

## Problem

Traditional LLM evaluation usually treats a model as a static text-to-text function, while an agent makes sequential decisions in an environment whose state changes after each action. A single final-answer score can hide tool-selection errors, wasted steps, unsafe intermediate behavior, brittle recovery, and differences caused by the agent harness rather than the backbone model. The field also contains many domain-specific benchmarks and commercial evaluation tools whose design choices are difficult to compare. The survey seeks a shared map that makes those evaluation targets, benchmark assumptions, and development practices legible.

## Research Area And Scenario

- Area(s): LLM and Agents.
- Scenario(s): Designing, selecting, and operating evaluations for tool-using agents, web agents, software-engineering agents, scientific agents, conversational agents, and generalist agents.
- Why it matters here: It is a focused review of agent evaluation rather than a broad survey of agent architecture, and it connects academic benchmarks with trajectory observability and continuous evaluation in development frameworks.
- Indexing rationale: It belongs in Agent Evaluation and Benchmark Design because it classifies both what an agent benchmark tests and the data, environment, interface, metric, and safety choices that shape the result.

## Assumptions And Scope

- Stated assumptions:
  - Claim: An LLM-based agent consists of a backbone LLM plus an agent harness and performs multi-step interaction with an environment.
    - Key constraint: The evaluation target is an **interactive agentic system**, not an isolated language-model response.
    - Why it matters: Planning, tool use, memory, observations, and recovery can change the outcome even when the backbone model is fixed.
    - Excludes or weakens: Static LLM benchmarks without a dynamic, agentic, or interactive component are outside the review's selection criteria.
  - Claim: Core capabilities may be evaluated independently or as part of an end-to-end workflow.
    - Key constraint: Isolated capability scores and system-level success measure **different units of analysis**.
    - Why it matters: A component benchmark can diagnose a weakness, but it cannot by itself predict performance after orchestration, prompting, tools, and environment feedback are combined.
    - Excludes or weakens: Treating function-calling accuracy, planning accuracy, or memory recall as a complete measure of agent quality.
  - Claim: Benchmark design can be compared along data curation, environment, interface, metric, and safety.
    - Key constraint: These are **cross-cutting design dimensions**, not a claim that every benchmark exposes all five equally well.
    - Why it matters: Two benchmarks that both report task success may differ materially in whether their worlds are static or dynamic, how actions are expressed, and whether unsafe success is penalized.
    - Excludes or weakens: Direct score comparisons across benchmarks without accounting for their evaluation contracts.
- Implied assumptions:
  - Claim: The selected literature is representative enough to support a field-level taxonomy.
    - Key constraint: The corpus is a **structured qualitative review**, not a quantitative meta-analysis.
    - Why it matters: Appendix A names repositories, queries, snowballing, criteria, and expert validation, but does not report a dated search log, screening counts, inter-rater agreement, or formal quality scoring.
    - Excludes or weakens: Claims of exhaustive coverage, publication-bias estimation, or reproducible effect-size synthesis.
- What is ignored or abstracted away:
  - Claim: The review compares benchmark and framework properties rather than normalizing their reported system scores.
    - Key constraint: Source results use **heterogeneous agents, models, tools, prompts, environments, and budgets**.
    - Why it matters: The survey can expose design patterns and gaps but cannot rank agent architectures causally.
    - Excludes or weakens: Inferring that a benchmark family or evaluation framework produces universally more valid scores.
- Applies when:
  - Claim: A reader needs to design an evaluation plan, diagnose an agent workflow, or understand the benchmark landscape across several agent domains.
    - Key constraint: The task benefits from a **cross-domain evaluation map** and explicit attention to trajectories and deployment tooling.
    - Why it matters: The taxonomy supplies a checklist for deciding what evidence an evaluation does and does not provide.
- May not apply when:
  - Claim: The reader needs a fully reproducible systematic review, a current registry of every benchmark, or controlled evidence about one agent implementation.
    - Key constraint: Agent evaluation is a **rapidly moving snapshot** and the survey is broad rather than experimentally comparative.
    - Why it matters: New benchmarks and platform features can postdate the paper, while individual benchmark validity still requires reading primary sources.

## Core Idea

The decisive reframing is that agent evaluation is evaluation of a closed-loop process, not merely evaluation of a generated answer. Once an agent can alter and observe an environment, correctness depends on the task distribution, world dynamics, action interface, intermediate trajectory, safety constraints, and operational cost. The survey therefore layers evaluation from capability to application and generality, then inspects the benchmark contract and the instrumentation used to observe the run. This explains why outcome, step, and trajectory signals are complementary and why backbone and harness effects eventually need to be varied independently.

## Method

The paper uses a multi-stage literature review and taxonomy-building procedure. Appendix A reports systematic searches across Google Scholar, ACL Anthology, Hugging Face Papers, and arXiv using agent-evaluation and benchmark keywords; forward and backward citation chasing; inclusion of work introducing a benchmark, evaluation framework, or significant evaluation method; exclusion of architecture-only work and static traditional LLM evaluation; and consultation with researchers from relevant subdomains. The selected works are then organized into the five perspectives summarized below.

- Component: Capability-evaluation map
  - Role: Separate foundational agent abilities from full-system task performance.
  - Input: Benchmarks and methods for planning, tool use, self-reflection, and memory.
  - Output: A capability-centered account of what can be tested in isolation and what remains entangled in an agent workflow.
  - Transformation: Groups evaluation work by the internal ability under examination and distinguishes static reasoning tests from interactive agentic assessment.
  - Minimal example: A function-calling benchmark can verify tool choice and argument construction without reproducing an entire web-agent task.
- Component: Application and generalist benchmark map
  - Role: Organize evaluations by the environments and task families in which agents act.
  - Input: Web, software-engineering, scientific, conversational, and cross-domain benchmarks and leaderboards.
  - Output: Domain-specific families plus a separate view of generalist evaluation.
  - Transformation: Compares how benchmark tasks, environments, and success conditions reflect different forms of real-world agency.
  - Minimal example: SWE-bench uses repository issues and executable tests, whereas a conversational benchmark can require policy-compliant state changes across dialogue and tool calls.
- Component: Core benchmark dimensions
  - Role: Make the evaluation contract of heterogeneous benchmarks comparable.
  - Input: Data provenance and curation, environment dynamicity, action and observation interface, metric, and explicit safety coverage.
  - Output: A five-dimension benchmark profile, illustrated for representative systems in Table 1.
  - Transformation: Re-expresses domain-specific testbeds in shared design terms so hidden differences behind task-success scores become visible.
  - Minimal example: A static cached web trace and a live browser can both test navigation, but only the latter exposes cascading consequences from an early wrong action.
- Component: Evaluation-framework taxonomy
  - Role: Describe how developers observe, score, debug, and compare agent runs across the lifecycle.
  - Input: Responses, individual steps, complete trajectories, production traces, annotations, judge outputs, and operational metrics.
  - Output: Final-response, stepwise, and trajectory assessments plus monitoring, human-in-the-loop review, synthetic data generation, and A/B comparison support.
  - Transformation: Aligns the granularity of evidence with the question being asked, from regression detection to failure localization and route quality.
  - Minimal example: A reference-based trajectory check compares tool calls with an expected path, while a reference-free LLM judge rates the observed route without requiring one gold sequence.

## Experiments And Evidence

- **Static versus dynamic environments → dynamic testbeds reveal compounding failures and long-horizon behavior that offline traces cannot represent.**
  - Selected evidence: Section 5 contrasts cached or trace-based settings such as the original Mind2Web with browser, container, and simulated worlds in which actions modify later observations.
  - Supports: Environment dynamicity is part of the construct being evaluated, not a neutral implementation detail.
  - Original: Section 5, “Environment,” and Table 1.
- **Final-response versus stepwise versus trajectory assessment → increasing granularity trades simplicity for diagnostic coverage.**
  - Selected evidence: Section 6 describes final-response checks as inexpensive but behavior-blind, stepwise checks as useful for localization but prone to ignoring cross-step dependencies, and trajectory checks as evaluating the route toward completion.
  - Supports: No single granularity is sufficient for both scalable monitoring and causal diagnosis of failures.
  - Original: Section 6, “Final Response Evaluation,” “Stepwise Evaluation,” and “Trajectory-Based Assessment.”
- **Reference-based versus reference-free trajectory evaluation → reproducibility and flexibility pull in opposite directions.**
  - Selected evidence: Reference matching supports exact, partial, unordered, subset, or graph-based alignment but requires expected behavior; LLM judges can assess coherence and efficiency without a gold path but introduce reliability and judge-specialization concerns.
  - Supports: Trajectory evaluation should report its reference and judge assumptions rather than presenting one opaque score.
  - Original: Section 6 and Table 2.
- **Cross-framework feature comparison → current tooling covers observability unevenly and still lacks scalable root-cause, evaluation-cost, and safety support.**
  - Selected evidence: Table 2 compares eight evaluation platforms on stepwise assessment, monitoring, trajectory assessment, human review, synthetic data, and A/B comparison; the accompanying discussion identifies gaps in aggregate diagnosis, causal attribution, evaluator cost, and policy compliance.
  - Supports: Evaluation infrastructure is useful but remains an active research target rather than a solved packaging problem.
  - Original: Section 6 and Table 2.
- Boundary note: The paper contributes synthesis rather than a new benchmark or controlled experiment. Its tables establish coverage and conceptual contrasts within the reviewed corpus; they do not provide normalized performance evidence across agents.

## Contributions

- Provides a focused, multi-perspective survey of LLM-agent evaluation spanning capabilities, applications, generalist systems, benchmark design, and development frameworks.
- Defines five cross-cutting benchmark dimensions: data curation, environment, interaction interface, metric, and safety.
- Distinguishes final-response, stepwise, and trajectory-level evaluation and explains the reference-based versus reference-free tradeoff.
- Documents the movement toward realistic, live, and adaptive benchmarks.
- Identifies research gaps in granular trajectory analysis, cost and efficiency, scalable automation, safety and compliance, and separation of backbone-model and harness contributions.

## Limitations

- The review is broad and representative rather than exhaustive; rapidly released benchmarks and changing commercial framework features can become outdated quickly.
- Appendix A gives meaningful search and selection detail but not a full reproducibility package with search dates, result counts, deduplication statistics, reviewer agreement, or formal study-quality scores.
- Many summarized artifacts are preprints, evolving leaderboards, software platforms, or product documentation, so their stability and evidential standards differ.
- The taxonomy describes relationships among evaluation layers but is not validated through user studies, inter-rater tests, or controlled comparisons of alternative taxonomies.
- Benchmark examples inherit validity, contamination, reproducibility, and implementation limitations from their primary sources.
- The survey can identify the need to decouple a backbone LLM from its harness, but does not itself supply a complete factorial protocol for doing so.

## Key Takeaways

- Evaluate an agent as a trajectory through a changing environment, not only as a final string.
- Record the benchmark's data, environment, interface, metric, and safety contract before interpreting its score.
- Combine end-to-end outcome measures with stepwise or trajectory evidence when the goal includes diagnosis rather than leaderboard ranking alone.
- Reference paths improve reproducibility but can penalize valid alternatives; judge-based evaluation expands coverage but requires reliability checks.
- Track latency, token and API cost, robustness across repeated runs, and policy compliance alongside task success.
- Compare backbone models and agent harnesses under controlled combinations whenever attributing performance gains.

## Relation: Previous Work

- Title: A survey on large language model based autonomous agents
  Authors: Lei Wang; Chen Ma; Xueyang Feng; Zeyu Zhang; Hao Yang; Jingsen Zhang; Zhiyuan Chen; Jiakai Tang; Xu Chen; Yankai Lin; Wayne Xin Zhao; Zhewei Wei; Jirong Wen.
  URL: https://doi.org/10.1007/s11704-024-40231-1
  Obsidian: [[wang2024autonomousagents]]
  - Type: survey
  - Status: ingested
  - Work summary: Wang et al. organize early LLM-agent research around profile, memory, planning, action, capability acquisition, applications, and evaluation.
  - Role: Broad architectural background that establishes why an LLM agent is more than its backbone model and surveys the earlier evaluation landscape.
  - Limitation: Evaluation is only one part of that survey, and its literature horizon is mainly the 2021–August 2023 wave.
  - This paper: Narrows the unit of analysis to evaluation and expands benchmark dimensions, trajectory assessment, framework tooling, and current research gaps.

## Relation: Compared With

- Title: Evaluation and Benchmarking of LLM Agents: A Survey
  Authors: Mahmoud Mohammadi; Yipeng Li; Jane Lo; Wendy Yip.
  URL: https://doi.org/10.1145/3711896.3736570
  Obsidian: [[mohammadi2025evaluation]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Mohammadi et al. organize agent evaluation by objectives—behavior, capabilities, reliability, and safety—and by process—interaction, data, metric computation, tooling, and context—with a dedicated enterprise section.
  - Similarity: Both reviews connect benchmarks, evaluation methods, tooling, cost, safety, and the need for continuous or scalable evaluation.
  - Difference: This paper provides a wider domain and benchmark map plus a disclosed review protocol; Mohammadi et al. offer a more compact what-versus-how taxonomy and stronger enterprise deployment framing.
  - When to use which: Use this paper for broad benchmark navigation and trajectory-level framework analysis; use Mohammadi et al. for an operational checklist centered on reliability, process, and enterprise constraints.
- Title: Evolutionary perspectives on the evaluation of LLM-based AI agents: a comprehensive survey
  Authors: Jiachen Zhu; Menghui Zhu; Renting Rui; Rong Shan; Congmin Zheng; Bo Chen; Yunjia Xi; Jianghao Lin; Weiwen Liu; Ruiming Tang; Yong Yu; Weinan Zhang.
  URL: https://doi.org/10.1007/s11704-026-51590-2
  Obsidian: [[zhu2027evolutionary]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Zhu et al. classify benchmarks by external environment and internal capability, then derive evolutionary trends and a two-stage benchmark-selection method.
  - Similarity: Both surveys treat dynamic environments, fine-grained process evidence, evaluator automation, efficiency, and safety as central to the future of agent evaluation.
  - Difference: This paper foregrounds evaluation layers and developer frameworks, while Zhu et al. foreground the chatbot-to-agent transition, environment/capability benchmark taxonomy, and future-aware selection.
  - When to use which: Use this paper to design a layered evaluation program; use Zhu et al. to locate environment- or capability-specific benchmarks and anticipate how the selection should evolve.

## Source Notes

- Metadata sources:
  - Official ACL Anthology record: https://aclanthology.org/2026.findings-acl.1330/
  - Official ACL Anthology PDF: https://aclanthology.org/2026.findings-acl.1330.pdf
  - arXiv record: https://arxiv.org/abs/2503.16416
  - Author-maintained companion repository: https://github.com/Asaf-Yehudai/LLM-Agent-Evaluation-Survey
- Source verification: The complete official PDF was inspected, including the taxonomy, benchmark and framework comparisons, discussion, Appendix A review methodology, capability appendix, and references. Bibliographic metadata was checked against the official ACL Anthology record and PDF first page, and the companion repository identity was checked against its GitHub record.
- PDF policy: The source PDF was acquired temporarily outside the repository and was not copied into the vault. Only stable legal source URLs are retained.

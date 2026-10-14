---
citekey: mohammadi2025evaluation
title: "Evaluation and Benchmarking of LLM Agents: A Survey"
authors:
  - Mahmoud Mohammadi
  - Yipeng Li
  - Jane Lo
  - Wendy Yip
year: 2025
venue: "KDD"
type: paper
status: ingested
tags:
  - llm-agents
  - agent-evaluation
  - survey
  - enterprise-ai
  - reliability
  - safety
topics:
  - Agent Evaluation
  - Benchmark Design
areas:
  - LLM
  - Agents
scenarios:
  - Agent Evaluation
  - Enterprise Agent Deployment
methods:
  - Literature Survey
  - Agent Evaluation Taxonomy
doi: "10.1145/3711896.3736570"
arxiv: "2507.21504"
official_url: "https://doi.org/10.1145/3711896.3736570"
pdf_url: "https://arxiv.org/pdf/2507.21504"
code_url: null
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 11-page author-posted PDF was inspected; its first page contains the final KDD 2025 citation, DOI, pagination statement, and ACM publication metadata."
  - "Title, author order, venue, DOI, and year were cross-checked against the final-form PDF, DOI record, arXiv record, and DBLP entry."
  - "The paper is a narrative taxonomy survey: it does not disclose literature-search databases, queries, screening criteria, review counts, or a study-quality procedure."
  - "No companion code or survey repository was identified from the paper or verified records."
created: 2026-09-14
updated: 2026-09-14
---

# Evaluation and Benchmarking of LLM Agents: A Survey

## Core Contribution

This survey proposes a two-axis framework that separates **what** an LLM agent evaluation targets from **how** the evaluation is run, and extends that framework to enterprise reliability, access control, long-horizon operation, and compliance. It turns a fragmented benchmark landscape into an operational checklist for evaluating agents before and after deployment.

## Summary

The paper begins from a practical problem: an agent can complete a task yet still be inconsistent, expensive, unsafe, or non-compliant, and an evaluation can look realistic while relying on a static dataset that misses production behavior. To prevent these concerns from being collapsed into one success score, the authors divide agent evaluation into two orthogonal questions.

- **Evaluation objectives—what to evaluate:** agent behavior, agent capabilities, reliability, and safety and alignment.
- **Evaluation process—how to evaluate:** interaction mode, evaluation data, metric-computation method, tooling, and evaluation context.

The objective axis moves from observable outcomes to internal competencies and operational trust. Behavior covers task completion, output quality, latency, and cost; capabilities cover tool use, planning and reasoning, memory and context retention, and multi-agent collaboration; reliability separates consistency from robustness; safety and alignment cover fairness, harm, bias, compliance, and privacy. The process axis then asks whether the test is offline or online, what data it uses, whether scores come from code, model judges, or humans, which platforms instrument the run, and how closely the test environment matches deployment.

The paper's distinctive contribution is its enterprise lens. Role-based access control means that correctness depends on who the agent represents; stochastic agents require repeated trials and stricter consistency metrics; long-running workflows expose drift and accumulated state; and policy compliance must be checked along the path, not inferred from a correct final answer. This motivates evaluation-driven development and AgentOps: evaluation becomes a continuous feedback loop across offline development tests and online monitoring rather than a one-time benchmark event.

## Problem

LLM-agent evaluations are spread across task benchmarks, capability tests, safety suites, judge methods, observability tools, and domain simulators, often without a common vocabulary. This fragmentation makes it easy to optimize for task completion while overlooking cost, repeated-run reliability, policy violations, or the mismatch between a controlled test and a live environment. Enterprise agents intensify the problem because permissions, auditability, private data, changing system state, and long-horizon consequences are part of correctness. The paper seeks a taxonomy that developers can use to specify both the target and the process of evaluation.

## Research Area And Scenario

- Area(s): LLM and Agents.
- Scenario(s): Offline benchmark testing, dynamic or online assessment, continuous AgentOps monitoring, and enterprise deployment under role-based access, reliability, privacy, and policy constraints.
- Why it matters here: The review connects academic evaluation objectives with the operational choices required to validate an agent in a real organization.
- Indexing rationale: It belongs in Agent Evaluation and Benchmark Design because its central artifact is a what-versus-how taxonomy, and in Enterprise Agent Deployment because it makes permissions, repeatability, long-horizon state, and compliance first-class evaluation concerns.

## Assumptions And Scope

- Stated assumptions:
  - Claim: Evaluation objectives and evaluation processes are distinct but complementary dimensions.
    - Key constraint: The taxonomy separates **what is measured** from **how evidence is produced**.
    - Why it matters: The same objective, such as task completion or safety, can be tested with different data, interaction modes, judges, and contexts, each changing the interpretation of the result.
    - Excludes or weakens: Treating a named benchmark or metric as a complete evaluation specification.
  - Claim: Offline and online evaluation should be used across the agent lifecycle.
    - Key constraint: Online evidence depends on **reactive simulations, users, or production monitoring** rather than inert test cases.
    - Why it matters: Static datasets are cheaper and reproducible, while live interactions reveal contextual failures, regressions, and changing use cases.
    - Excludes or weakens: Assuming that a one-time pre-deployment score remains representative after the agent, tools, users, or environment change.
  - Claim: Enterprise evaluation must model role and policy constraints in addition to task success.
    - Key constraint: Allowed behavior is **identity- and domain-dependent** through RBAC, approval flows, privacy rules, and regulation.
    - Why it matters: An action can reach the desired state while accessing forbidden data or bypassing an organizational requirement.
    - Excludes or weakens: Benchmarks whose environment has no permission model or whose scorer ignores the path taken to success.
- Implied assumptions:
  - Claim: The cited benchmarks and frameworks are representative enough to populate the taxonomy.
    - Key constraint: Selection is based on an **undocumented narrative-review process**.
    - Why it matters: The paper does not report databases, queries, dates, inclusion criteria, screening counts, or formal quality assessment.
    - Excludes or weakens: Claims that the survey is exhaustive, reproducible as a systematic review, or statistically representative of the field.
  - Claim: Taxonomy categories can transfer across domains and modalities.
    - Key constraint: Concrete metrics still require **domain-specific operationalization**.
    - Why it matters: Planning quality, fairness, policy adherence, and user satisfaction do not have one universal measurement procedure.
    - Excludes or weakens: Reusing a metric across healthcare, finance, software engineering, and web automation without validating its meaning.
- What is ignored or abstracted away:
  - Claim: The review maps metrics and benchmark examples but does not normalize their agents, models, prompts, budgets, or task difficulty.
    - Key constraint: Evidence is **heterogeneous and non-comparative** across cited studies.
    - Why it matters: A category table helps design coverage but cannot identify the best agent or establish causal effects of individual components.
    - Excludes or weakens: Cross-paper performance ranking and quantitative synthesis.
- Applies when:
  - Claim: A team needs an evaluation checklist spanning development, deployment, operations, reliability, and governance.
    - Key constraint: The agent operates in a **multi-step, tool-using, or organizational setting** where outcome alone is insufficient.
    - Why it matters: The two-axis framework helps reveal missing evidence before a system is declared ready.
- May not apply when:
  - Claim: The task requires a systematic literature review, benchmark-by-benchmark validity audit, or a formally specified compliance test suite.
    - Key constraint: The paper supplies a **conceptual taxonomy**, not a reproducible evidence protocol or implementation standard.
    - Why it matters: Primary benchmark papers, domain rules, and deployment-specific threat models remain necessary.

## Core Idea

The central insight is that agent evaluation becomes manageable when the target and the procedure are specified separately. “Can the agent finish?” is an objective; “under a static test, a dynamic simulator, or live traffic, scored by assertions, an LLM judge, or experts” describes the evidence process. Crossing these axes exposes blind spots: a system can have strong behavioral scores but poor repeated-run consistency, or a realistic context but a judge that cannot verify policy compliance. The enterprise extension follows naturally because identity, permissions, state, and policy alter both the valid action space and the evidence needed to establish trustworthy behavior.

## Method

The authors synthesize prior benchmarks, evaluation studies, and developer tooling into a hierarchical taxonomy. They first enumerate four objective families and their representative metrics, then describe five process choices. The framework is used to discuss enterprise-specific gaps and to derive four future directions: holistic, realistic, automated and scalable, and time- and cost-bounded evaluation. The paper does not report a literature-search or study-selection protocol, so its method should be understood as expert narrative synthesis rather than systematic review.

- Component: Evaluation-objective taxonomy
  - Role: Specify the property of the agent that the evidence is intended to support.
  - Input: Task outcomes, generated outputs, execution traces, repeated runs, perturbations, safety events, policy constraints, and user or evaluator judgments.
  - Output: Evidence grouped under behavior, capabilities, reliability, and safety and alignment.
  - Transformation: Moves from black-box utility to process competencies and then to trustworthiness under repeated or adverse conditions.
  - Minimal example: A web agent can be measured for task success under behavior, correct tool selection under capability, pass^k under reliability, and forbidden-data access under compliance.
- Component: Evaluation-process taxonomy
  - Role: Specify how test cases are generated, executed, observed, and scored.
  - Input: Static datasets or live interactions, human or synthetic data, rules or judges, instrumentation, and controlled or open-world environments.
  - Output: An evaluation design described by interaction mode, data, metric computation, tooling, and context.
  - Transformation: Converts an objective into an executable evidence pipeline and exposes tradeoffs in realism, reproducibility, flexibility, cost, and security.
  - Minimal example: A deterministic code assertion in a sandbox provides repeatable evidence for a structured task, while a production dialogue reviewed by a domain expert covers nuance at much greater cost.
- Component: Enterprise constraint analysis
  - Role: Extend generic evaluation to conditions that determine whether an agent is deployable in an organization.
  - Input: User roles, permissions, domain policies, repeated stochastic runs, long-lived state, private data, and compliance requirements.
  - Output: Requirements for role-aware test generation, reliability guarantees, long-horizon evaluation, and policy-aware scoring.
  - Transformation: Redefines success from reaching the goal state to reaching it consistently through authorized and auditable behavior.
  - Minimal example: A financial-reporting agent must produce the report without reading forecasts unavailable to the represented user and while following the required approval path.
- Component: Evaluation-driven development and AgentOps loop
  - Role: Keep evidence current as the agent and its operating environment evolve.
  - Input: Offline regression suites, online interactions, production traces, operational metrics, and detected failures.
  - Output: Monitoring signals and cases that feed back into development and subsequent evaluation.
  - Transformation: Integrates testing into the lifecycle instead of treating evaluation as a terminal benchmark run.
  - Minimal example: A newly observed production failure becomes an offline regression case while online monitors continue checking latency, quality, and safety.

## Experiments And Evidence

- **Outcome success versus consistency → occasional success is not a reliability guarantee.**
  - Selected evidence: Sections 3.3 and 5.2 distinguish pass@k, where at least one of k attempts succeeds, from pass^k, where all k attempts must succeed; the paper uses τ-Bench as a representative repeated-run evaluation.
  - Supports: Deployment readiness requires repeated-run evidence that penalizes stochastic brittleness rather than rewarding lucky success.
  - Original: Sections 3.3.1 and 5.2; Table 1.
- **Static/offline versus dynamic/online evaluation → reproducibility and realism address different risks.**
  - Selected evidence: Section 4.1 contrasts inert datasets with reactive simulators, humans, and production monitoring, noting that online data can reveal domain-context failures missed during static testing.
  - Supports: Evaluation should progress from controlled regression tests toward realistic interaction as system maturity and risk permit.
  - Original: Sections 4.1.1 and 4.1.2.
- **Code-based, LLM-based, and human scoring → evaluator choice trades determinism, scope, reliability, and cost.**
  - Selected evidence: Section 4.3 characterizes code assertions as reproducible but inflexible, LLM-as-a-Judge and Agent-as-a-Judge as scalable and adaptable, and human review as strong for subjective or safety-critical judgments but expensive.
  - Supports: Scoring methods should be matched to claim type and cross-checked when open-ended or high-stakes judgments are involved.
  - Original: Section 4.3.
- **Generic versus enterprise evaluation → permissions and policy change the definition of correctness.**
  - Selected evidence: Section 5 analyzes RBAC, repeated-run reliability, long-horizon interactions, and domain policy; examples include identity-aware task generation and constraints such as approval workflows, GDPR, and HIPAA.
  - Supports: Enterprise benchmarks need stateful authorization and compliance checks, not only task-level outputs.
  - Original: Sections 5.1–5.4.
- Boundary note: Table 1 maps objectives to metrics and representative papers but is not a controlled comparison. The survey reports no original benchmark runs, ablations, user study, or quantitative meta-analysis.

## Contributions

- Introduces a compact two-dimensional taxonomy separating evaluation objectives from evaluation processes.
- Integrates behavior, capability, reliability, safety, interaction mode, data, judges, tooling, and context into one evaluation design vocabulary.
- Elevates repeated-run consistency and pass^k as distinct from best-of-several success.
- Identifies RBAC, reliability guarantees, dynamic long-horizon interaction, and domain policy as enterprise-specific evaluation requirements.
- Connects offline benchmarks, online monitoring, evaluation-driven development, and AgentOps into a lifecycle view.
- Derives future needs for holistic, realistic, automated, scalable, and time- and cost-bounded evaluation.

## Limitations

- The paper does not disclose a literature-search, screening, deduplication, or quality-assessment method, so it should be classified as a narrative taxonomy survey rather than a systematic review.
- It introduces no original benchmark, experiment, or validation study showing that teams apply the taxonomy consistently or that it improves deployment outcomes.
- Table 1 contains a broad mapping of metrics and papers but cannot normalize differences in tasks, models, environments, prompting, or evaluation budgets.
- Several tool and platform examples are fast-changing software products whose capabilities may differ after publication.
- Enterprise challenges are well motivated but remain conceptual; the paper does not define an executable RBAC model, audit schema, legal-compliance oracle, or threat model.
- LLM-as-a-Judge and Agent-as-a-Judge are presented as scalable directions without a detailed treatment of judge bias, calibration, correlated failure, or adversarial manipulation.

## Key Takeaways

- Specify both the evaluation objective and the evidence process; neither dimension alone defines a valid test.
- Measure task outcome, capability, reliability, safety, latency, and cost as distinct properties rather than compressing them prematurely.
- Use pass^k when the requirement is consistent success across repeated runs, and do not confuse it with pass@k.
- Combine offline regression tests with dynamic simulations or online monitoring as the agent approaches deployment.
- In enterprise settings, include user identity, permissions, workflow state, and policy adherence in the task and scorer.
- Treat evaluation as a continuous development and operations loop, especially for agents whose tools, data, or behavior evolve.

## Relation: Previous Work

- Title: A survey on large language model based autonomous agents
  Authors: Lei Wang; Chen Ma; Xueyang Feng; Zeyu Zhang; Hao Yang; Jingsen Zhang; Zhiyuan Chen; Jiakai Tang; Xu Chen; Yankai Lin; Wayne Xin Zhao; Zhewei Wei; Jirong Wen.
  URL: https://doi.org/10.1007/s11704-024-40231-1
  Obsidian: [[wang2024autonomousagents]]
  - Type: survey
  - Status: ingested
  - Work summary: Wang et al. provide a broad early map of LLM-agent architecture, capability acquisition, applications, and evaluation.
  - Role: Supplies general agent context and an earlier evaluation taxonomy that distinguishes subjective and objective evidence.
  - Limitation: Evaluation is only one component of the broader survey and its primary literature horizon precedes many enterprise-oriented benchmarks.
  - This paper: Focuses on evaluation as the main object and adds process design, continuous operations, reliability, access control, and compliance.
- Title: A Survey on Evaluation of LLM-based Agents
  Authors: Asaf Yehudai; Lilach Eden; Alan Li; Guy Uziel; Yilun Zhao; Roy Bar-Haim; Arman Cohan; Michal Shmueli-Scheuer.
  URL: https://arxiv.org/abs/2503.16416
  Obsidian: [[yehudai2026evaluation]]
  - Type: survey
  - Status: ingested
  - Work summary: Yehudai et al. map capability, application-specific, generalist, benchmark-design, and framework perspectives on LLM-agent evaluation.
  - Role: The paper cites the arXiv version as a contemporary agent-evaluation survey and uses it to situate the need for a complementary taxonomy.
  - Limitation: Its breadth and detailed benchmark map make it less compact as an enterprise evaluation checklist.
  - This paper: Reorganizes the field around what is evaluated and how the evaluation is conducted, then emphasizes organizational deployment constraints.

## Relation: Compared With

- Title: Evolutionary perspectives on the evaluation of LLM-based AI agents: a comprehensive survey
  Authors: Jiachen Zhu; Menghui Zhu; Renting Rui; Rong Shan; Congmin Zheng; Bo Chen; Yunjia Xi; Jianghao Lin; Weiwen Liu; Ruiming Tang; Yong Yu; Weinan Zhang.
  URL: https://doi.org/10.1007/s11704-026-51590-2
  Obsidian: [[zhu2027evolutionary]]
  - Type: compares-with
  - Status: ingested
  - Work summary: Zhu et al. distinguish agents from chatbots, organize benchmarks by environment and capability, and propose present- and future-oriented benchmark selection.
  - Similarity: Both reviews provide orthogonal evaluation axes, cover tools, planning, memory, multi-agent behavior, dynamic contexts, judge automation, efficiency, and safety.
  - Difference: This paper is organized around evaluation objectives and operational process with an enterprise focus; Zhu et al. are organized around agent evolution, benchmark families, and selection guidance.
  - When to use which: Use this paper to build a lifecycle and governance checklist; use Zhu et al. to browse benchmark families and plan how an evaluation portfolio should mature with the agent.

## Source Notes

- Metadata sources:
  - DOI record: https://doi.org/10.1145/3711896.3736570
  - Legal open arXiv record: https://arxiv.org/abs/2507.21504
  - Legal open arXiv PDF: https://arxiv.org/pdf/2507.21504
  - DBLP record: https://dblp.org/rec/conf/kdd/MohammadiLLY25.html
- Source verification: The complete author-posted PDF was inspected, including the final KDD citation block, taxonomy, objective and process sections, enterprise analysis, future directions, table, and references. Bibliographic metadata was cross-checked with the DOI, arXiv, and DBLP records.
- PDF policy: The source PDF was acquired temporarily outside the repository and was not copied into the vault. Only stable legal source URLs are retained.

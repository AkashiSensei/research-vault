---
citekey: yao2023react
title: "ReAct: Synergizing Reasoning and Acting in Language Models"
authors:
  - Shunyu Yao
  - Jeffrey Zhao
  - Dian Yu
  - Nan Du
  - Izhak Shafran
  - Karthik Narasimhan
  - Yuan Cao
year: 2023
venue: "ICLR 2023"
type: paper
status: ingested
tags:
  - large-language-models
  - autonomous-agents
  - reasoning
  - tool-use
  - prompting
  - interactive-decision-making
topics:
  - Reasoning and Acting
  - Tool Use
areas:
  - LLM
  - Agents
scenarios:
  - Knowledge-Intensive Reasoning
  - Interactive Decision Making
methods:
  - ReAct
  - Few-Shot Prompting
doi: null
arxiv: "2210.03629"
official_url: "https://openreview.net/forum?id=WE_vluYUL-X"
pdf_url: "https://arxiv.org/pdf/2210.03629"
code_url: "https://github.com/ysymyth/ReAct"
local_pdf: null
source_acquired: true
source_verified_against_original: true
verification_status: verified
analysis_confidence: high
verification_notes:
  - "The complete 33-page arXiv v3 ICLR camera-ready paper was inspected, including appendices, prompts, fine-tuning details, example trajectories, and failure analysis."
  - "Title, author order, ICLR 2023 publication status, arXiv identifier, project page, and code repository were cross-checked across the paper, arXiv, OpenReview identity, DBLP, and the author project."
  - "The publication year is 2023 even though the first arXiv version appeared in 2022."
  - "The principal method is few-shot prompting of frozen PaLM-540B; the separate PaLM-8B and PaLM-62B trajectory fine-tuning experiment is not treated as the main ReAct setup."
  - "No ICLR publisher DOI was identified; the arXiv DataCite DOI is not stored as a venue DOI."
created: 2026-07-29
updated: 2026-08-09
---

# ReAct: Synergizing Reasoning and Acting in Language Models

## Summary

> **User-directed revision:** Expanded the summary to clarify ReAct's unified loop and natural-language programmability, then added how task-family-specific action interfaces and example-guided Thought behavior are specified.

ReAct's central contribution is not the invention of the agent loop itself. It expresses that loop as a minimal, language-native protocol in which one pretrained LLM can reason, act, incorporate feedback, and decide how to continue.

### Mechanism At A Glance

| Element | Role in the trajectory |
| --- | --- |
| Task + history | Supplies the current goal and accumulated context for the next decision. |
| Thought | Adds free-form reasoning to the language context without directly changing the environment. |
| Action | Invokes a task-specific tool or affects the external environment. |
| Observation | Returns the result of an external action and grounds the model's next decision. |

1. The model reads the task and the accumulated Thought/Action/Observation trajectory.
2. It generates the next thought or task action.
3. A thought updates the language trajectory; an external action is executed and yields an observation.
4. The new result is appended to the history, and the loop repeats until the model selects a terminating action.

**Compact flow:** Task + history → choose Thought or Action → update language context or receive Observation → append result → repeat or finish.

### What ReAct Changes

- **A unified trajectory:** Reasoning, acting, and environmental feedback become parts of one autoregressive sequence rather than separate modules connected by a domain-specific pipeline.
- **A fixed outer loop with a dynamic inner path:** Code defines the interaction shell, but the LLM decides at runtime whether the next useful step is more reasoning, an external action, or termination.
- **An expanded action space:** The paper effectively augments environment actions with language-space reasoning actions, allowing internal deliberation to influence later external behavior.
- **No new policy training in the main setup:** The principal experiments use few-shot prompting of frozen PaLM-540B, showing that the protocol can be induced through demonstrations rather than requiring a newly trained architecture.

### How The Prompt Specifies Thought

- **The protocol is shared, not the complete prompt:** ReAct provides the common Thought/Action/Observation interaction pattern, while each environment or task family still needs an adapted action interface: allowed operations, syntax, observation format, and completion conditions. An interface can be reused across related tasks, so it is not redesigned for every instance.
- **Thought strategy is taught mainly by example:** The original prompts do not give one cross-task checklist for what every Thought must do. Instead, task-specific few-shot trajectories implicitly demonstrate useful behaviors such as decomposing a question, extracting evidence, reformulating a search, tracking subgoals, or deciding what to do next.
- **Examples are the main guide, not the only constraint:** The task objective, Thought/Action labels, available actions, and accumulated observations also shape the model's behavior, but the demonstrations carry most of the procedural guidance about how to reason.
- **Design happens at the task-family level, generation at the instance level:** Prompt authors adapt the interface and curate representative reasoning trajectories; the LLM then generates each concrete Thought from the current task and history. Knowledge tasks demonstrate dense Thought/Action steps, whereas long-horizon decision tasks use sparse Thoughts at strategically useful points.

**In short:** ReAct explicitly defines what the agent can do, while mainly demonstrating—rather than prescribing—how it should think.

### Why It Matters

- **Analytical interpretation — natural-language programming:** Prompt instructions, demonstrations, and serialized history can program part of the agent's control policy in natural language. Changing them can change how the model decomposes a task, chooses tools, handles ambiguity, and decides when to stop.
- **One context serves several roles:** The language trajectory acts simultaneously as working memory, planning space, state interpretation, and a coordination interface between the model and its tools.
- **Adaptive behavior needs less hand-authored workflow logic:** Instead of prescribing every intermediate stage in code, a developer can provide the available actions and interaction rules while allowing the LLM to compose the actual path.
- **Soft control is not hard control:** Tool execution, permissions, allowed actions, validation, safety constraints, and hard stopping conditions still belong to the external controller.

### Evidence And Boundary

- The paper evaluates ReAct on Wikipedia-grounded question answering and fact verification, plus text-only ALFWorld and WebShop environments.
- Reasoning improves planning and state tracking, while observations ground subsequent reasoning and enable course correction.
- Plain ReAct is not uniformly best: hybrid routing with self-consistent chain-of-thought performs best on the knowledge tasks.
- Performance still depends on a capable base model, useful demonstrations, and well-designed task-specific action interfaces.

## Problem

Chain-of-thought reasoning relies on the model's internal knowledge and cannot directly query or affect the world, so an incorrect premise can propagate through an otherwise coherent trace. Action-only language agents can retrieve information or manipulate an environment, but they often struggle to decompose goals, track state, synthesize observations, decide what information to obtain, and recover from mistakes. ReAct asks whether one language-model policy can alternate reasoning and acting so each behavior corrects the other's weakness.

## Research Area And Scenario

- Area(s): LLM and Agents.
- Scenario(s): Knowledge-intensive multi-hop reasoning with a restricted Wikipedia API, fact verification, long-horizon household tasks in text-only ALFWorld, and simulated e-commerce interaction in WebShop.
- Why it matters here: ReAct is a foundational mechanism-level paper that makes the abstract agent loop concrete: a model forms a thought, selects an action, receives an observation, and revises its next step.
- Indexing rationale: It belongs in LLM because a prompted language model implements the policy, in Agents because that policy acts over time in an environment, in Knowledge-Intensive Reasoning because retrieval grounds QA and verification, and in Interactive Decision Making because ALFWorld and WebShop require long-horizon state tracking and action selection.

## Assumptions And Scope

- Stated assumptions:
  - Claim: The principal results use few-shot prompting of a frozen language model.
    - Key constraint: **PaLM-540B** with no parameter update in the main experiments.
    - Why it matters: ReAct is introduced as a prompting and interaction protocol, not as reinforcement learning or a new trained architecture.
    - Excludes or weakens: Claims that the method autonomously learns a tool schema from rewards or that its main gains come from weight updates.
  - Claim: Each task supplies a hand-designed textual action space.
    - Key constraint: **task-specific actions**, such as `search`, `lookup`, and `finish` for Wikipedia or simulator-specific commands for ALFWorld and WebShop.
    - Why it matters: The common contribution is the thought/action protocol, not a universal tool interface.
    - Excludes or weakens: Zero-shot discovery of arbitrary tools, unrestricted operating-system control, and open-ended live-web action.
  - Claim: Few-shot trajectories communicate both the action syntax and useful reasoning behavior.
    - Key constraint: **manual demonstrations** that fit in the context window.
    - Why it matters: Prompt construction is part of the method's data and engineering cost.
    - Excludes or weakens: Fully zero-shot use and environments whose action inventory or demonstrations exceed available context.
  - Claim: The environment returns textual observations that can be appended to model context.
    - Key constraint: **text-only interfaces**.
    - Why it matters: ReAct does not itself solve perception or low-level control; it reasons over already textual state.
    - Excludes or weakens: Visual browsing, multimodal grounding, physical robot control, and sensorimotor learning.
- Implied assumptions:
  - Claim: The base model already has strong language, commonsense, decomposition, and instruction-following abilities.
    - Key constraint: **large pretrained model capacity**.
    - Why it matters: Prompted PaLM-8B and PaLM-62B struggle to learn both reasoning and acting from context, while PaLM-540B performs substantially better.
    - Excludes or weakens: Small or weak models without task-relevant priors.
  - Claim: Visible thoughts are useful diagnostic artifacts.
    - Key constraint: **trace interpretability, not proven faithfulness**.
    - Why it matters: The paper manually labels failures and demonstrates one direct thought edit, but it does not prove that generated thoughts faithfully expose all latent computation.
    - Excludes or weakens: Strong claims that the trace is a complete causal explanation or a validated user-facing safety mechanism.
- What is ignored or abstracted away:
  - Claim: Safety and reliability are studied only inside restricted benchmark environments.
    - Key constraint: **sandboxed text tasks** with no real purchase, private data, unrestricted browsing, or physical consequence.
    - Why it matters: The action loop can expose privacy and harmful-action risks when connected to broader tools, but those risks are acknowledged rather than tested.
    - Excludes or weakens: Deployment claims for consequential, open-world agents.
- Applies when:
  - Claim: A capable LLM can interact through a defined textual interface and useful example trajectories are available.
    - Key constraint: **bounded action space and observable text feedback**.
    - Why it matters: In that setting, reasoning can guide retrieval or action and observations can correct the next step without training a new policy.
- May not apply when:
  - Claim: The environment is poorly observed, the action grammar is unknown, demonstrations are unavailable, or repeated mistakes are costly.
    - Key constraint: **open-ended or safety-critical deployment**.
    - Why it matters: ReAct can loop, issue ineffective searches, hallucinate progress, or choose a wrong action, and the paper supplies no general recovery or safety layer.

## Core Idea

ReAct expands the external action space \(\mathcal{A}\) with a natural-language thought space \(\mathcal{L}\). A thought changes only the textual context; an action queries or changes the environment and returns an observation. Alternating the two creates a closed loop in which reasoning determines what to do or retrieve next, while observations ground, revise, or invalidate later reasoning.

## Method

The prompt contains human-written trajectories composed of thoughts, domain actions, and environment observations. At inference time, the frozen language model consumes the prompt, task, and accumulated trajectory, then emits either another thought or a valid action. The action executes through the domain adapter, its textual result is appended, and the model continues until it emits a terminal action or reaches a step limit.

The schedule changes with task structure. HotpotQA and FEVER use dense thought–action–observation alternation because each retrieval should inform the next reasoning step. ALFWorld and WebShop use sparse thoughts so a long trajectory can perform several routine actions between higher-level planning and state-tracking updates. The paper also tests heuristic hybrids that switch between ReAct and self-consistent chain-of-thought when one method appears uncertain or stuck.

- Component: Few-shot trajectory prompt
  - Role: Specify the desired reasoning style, action syntax, and interaction pattern without updating parameters.
  - Input: Solved tasks annotated with thoughts, actions, and resulting observations.
  - Output: In-context exemplars prepended to a new task.
  - Transformation: Demonstrates how internal reasoning and environment interaction should alternate.
  - Minimal example: A QA exemplar shows `Thought: identify the missing entity`, `Search[entity]`, an observation, `Lookup[relation]`, and `Finish[answer]`.
- Component: ReAct language-model policy
  - Role: Choose the next thought or executable action from the full textual trajectory.
  - Input: Few-shot prompt, current task, all previous thoughts, actions, and observations.
  - Output: A free-form thought or a task-valid action.
  - Transformation: Uses the current context to decompose goals, track subgoals, synthesize evidence, reformulate searches, or select an action; most main experiments decode greedily.
  - Minimal example: After a search result identifies the High Plains, the model states that it must find that region's elevation and searches the newly identified entity.
- Component: Wikipedia action interface
  - Role: Ground knowledge-intensive reasoning in external text.
  - Input: `search[entity]`, `lookup[string]`, or `finish[answer]`.
  - Output: The first five sentences of an exact page, similar-entity suggestions, the next sentence containing a lookup term, or a final answer.
  - Transformation: Converts a model-selected query into a small evidence snippet appended to the trajectory.
  - Minimal example: Search an entity, then look up the phrase “named after” to recover a specific relation needed by the question.
- Component: ALFWorld and WebShop interfaces
  - Role: Test long-horizon action selection, subgoal tracking, and recovery in interactive text environments.
  - Input: A text goal, current observation, and domain commands such as navigate, open, take, clean, put, search, select, or buy.
  - Output: Updated textual state, product information, reward, or success/failure feedback.
  - Transformation: Sparse thoughts turn a high-level goal into subgoals and use observations to decide whether to continue, revise, or terminate.
  - Minimal example: For “put a cleaned object on a table,” the agent reasons about a likely object location, retrieves it, navigates to a sink, cleans it, and places it while marking subgoals complete.
- Component: CoT-SC/ReAct routing
  - Role: Exploit the complementary strengths of internal reasoning and external grounding.
  - Input: A ReAct step budget or agreement among sampled chain-of-thought answers.
  - Output: Continue with the current method or fall back to the other method.
  - Transformation: Switches from ReAct after seven HotpotQA or five FEVER steps without an answer, or switches from CoT-SC when the modal answer lacks majority support.
  - Minimal example: Low agreement among internal answers triggers Wikipedia interaction before producing the final answer.
- Optional component: Bootstrapped trajectory fine-tuning
  - Role: Test whether the ReAct format can teach smaller models when parameter updates are allowed.
  - Input: 3,000 correct model-generated trajectories per target format.
  - Output: Fine-tuned PaLM-8B or PaLM-62B trajectory decoders.
  - Transformation: Applies supervised learning to complete thought, action, and observation trajectories conditioned on a task.
  - Minimal example: A smaller model is trained to reproduce successful ReAct trajectories instead of inferring the protocol from a few prompt examples.

## Experiments And Evidence

> **User-directed revision:** Reorganized the evidence as a comparison map with representative results, supported conclusions, original-paper locations, qualitative evidence, and explicit reading boundaries.

The experiments are best read as a comparison map. Each item below states what
is compared, what that comparison supports, and where to return to the original
paper. The wording uses “supports” rather than “proves” because several results
are prompt-sensitive or qualitative.

### Core Comparison Map

- **ReAct vs. Act → reasoning makes action more purposeful.**
  - Selected evidence: ReAct improves ALFWorld success from 45% to 71% in the
    best prompt trials and WebShop success from 30.1% to 40.0%. The same
    direction also appears on HotpotQA and FEVER.
  - Supports: Thoughts help decompose goals, interpret observations, track
    state, choose the next action, and synthesize the final answer.
  - Original: Section 3.3, Table 1; Section 4, Tables 3–4; example trajectories
    in Appendix D.2–D.3.

- **ReAct vs. CoT → external action can ground reasoning, but introduces new
  failure modes.**
  - Selected evidence: ReAct beats CoT on FEVER (60.9 vs. 56.3) but trails it
    slightly on HotpotQA (27.4 vs. 29.4). The manual audit finds less
    hallucination with ReAct, but more search failures and repetitive loops.
  - Supports: Retrieval and observations improve factual grounding, but acting
    is valuable only when the action returns useful evidence and the model can
    recover from it.
  - Original: Section 3.3, Tables 1–2.

- **ReAct/CoT-SC hybrids vs. either method alone → internal and external
  knowledge are complementary.**
  - Selected evidence: The two fallback routes give the strongest prompting
    results on HotpotQA and FEVER and can match 21-sample CoT-SC with roughly
    3–5 samples.
  - Supports: A useful system should reason internally when possible and seek
    external evidence when uncertain or stuck.
  - Original: Section 3.2, “Combining Internal and External Knowledge”;
    Section 3.3, Table 1 and Figure 2.

- **ReAct vs. ReAct-IM → adding Thought text is not enough; the content and
  freedom of reasoning matter.**
  - Selected evidence: ReAct reaches 71% ALFWorld success versus 53% for
    ReAct-IM in the best prompt trials and wins on five of six task types.
  - Supports: Effective thoughts perform state interpretation, subgoal
    completion and switching, commonsense invocation, and plan updating rather
    than merely restating the current goal.
  - Original: Section 4, Table 3 and “On the value of internal reasoning vs.
    external feedback”; Appendix B.2 and D.2.3.

- **Different ALFWorld prompt permutations and trained baselines vs. few-shot
  ReAct → ReAct shows prompt robustness and strong task-level few-shot transfer.**
  - Selected evidence: ReAct beats Act in all six controlled prompt trials; the
    worst ReAct trial still exceeds the best Act trial. Two-shot ReAct also
    outperforms the reported trained BUTLER baseline, while one-shot ReAct
    outperforms IL and IL+RL on WebShop.
  - Supports: A capable pretrained LLM can acquire the task interaction pattern
    from very few demonstrations.
  - Original: Section 4, ALFWorld/WebShop setup and results; Tables 3–4.

- **Prompting across model sizes vs. fine-tuning → prompt-time ReAct needs model
  capacity, while training can internalize the protocol.**
  - Selected evidence: Prompted PaLM-8B and PaLM-62B struggle with ReAct, but
    after training on 3,000 successful trajectories, fine-tuned ReAct becomes
    the best tested format for both sizes and can outperform larger prompted
    models.
  - Supports: ReAct can be elicited from a sufficiently capable frozen model or
    learned through task-specific supervision; it is not equally easy to obtain
    from every base model through prompting alone.
  - Original: Section 3.2, “Finetuning”; Section 3.3, Figure 3; Appendix B.1.

- **The same protocol across knowledge and decision tasks, and PaLM vs. GPT-3 →
  ReAct has protocol-level generality.**
  - Selected evidence: Dense ReAct is used for HotpotQA/FEVER, sparse ReAct for
    ALFWorld/WebShop, and the appendix reports successful GPT-3 results as well
    as PaLM results.
  - Supports: The thought/action/observation protocol can adapt to different
    reasoning schedules and is not tied to one model family.
  - Original: Section 2; Sections 3–4; Appendix A.1, Table 5.

### Qualitative Supporting Evidence

- **Standard/CoT/Act vs. ReAct on an outdated HotpotQA case → current
  information requires both retrieval and reasoning.**
  - ReAct alone obtains the updated answer; this is an illustrative case, not a
    dataset-wide result.
  - Original: Appendix A.2, Figure 4.

- **Original vs. human-edited ReAct trajectory → visible thoughts provide a
  control and debugging surface.**
  - Editing two thoughts redirects the downstream actions and turns one failed
    ALFWorld trajectory into a success.
  - Original: Appendix A.3, Figure 5.

### Reading Boundaries

- The 71% ALFWorld result is best-of-six; the reported ReAct average is 57%.
- The hybrid ReAct/CoT-SC switches are implemented by external heuristics, not
  learned autonomously by the LLM.
- Few-shot comparisons do not include the large language model's pretraining
  cost and are not fully matched against trained baselines.
- The outdated-knowledge and human-edit examples are qualitative evidence.
- Supervised domain-specific systems remain much stronger on HotpotQA and
  FEVER, and humans remain stronger on WebShop.

## Contributions

- Defines a simple language-model policy that unifies natural-language thoughts and executable actions in one trajectory.
- Demonstrates across four text tasks that reasoning can improve action selection and state tracking while external observations can reduce unsupported reasoning.
- Provides Standard, CoT, Act, and ReAct ablations plus self-consistency hybrids, prompt-permutation analysis, and an IM-style prompting comparison.
- Uses visible trajectories for failure diagnosis and shows a qualitative example of direct human thought editing.
- Provides initial evidence that successful ReAct trajectories can supervise smaller models when training is available.

## Limitations

- Prompted ReAct depends on a very large and capable pretrained model; smaller prompted PaLM variants perform poorly.
- Every environment requires a manually designed action grammar, adapter, and few-shot trajectories.
- Plain ReAct is not uniformly superior, and the best knowledge-task results rely on heuristic switching with self-consistent chain-of-thought.
- The main environments are English-language, text-only, benchmark-controlled, and much narrower than unrestricted web or physical deployment.
- Search misses, repetitive reasoning/action loops, hallucinated thoughts, mistaken completion tracking, and weak recovery remain common.
- Several headline results use best-of-prompt reporting, and the paper provides no confidence intervals or statistical-significance tests.
- Visible thoughts are not shown to be faithful explanations, and thought editing is demonstrated with one qualitative success rather than a controlled user study.
- PaLM-540B is not publicly available, limiting exact reproduction even though prompts, project material, and code for other models are provided.
- The safety discussion acknowledges privacy and harmful-action risks but evaluates only restricted sandboxes with no real purchases, private information, or physical consequences.

## Key Takeaways

- ReAct is primarily a prompting and interaction protocol, not a reinforcement-learning or model-training algorithm.
- Its core value is the closed textual loop: reasoning chooses an action, the action produces evidence, and that evidence changes later reasoning.
- Reasoning-only and externally grounded methods are complementary rather than universally ordered; the hybrid results make this explicit.
- The method's apparent generality depends on task-specific text adapters and demonstrations.
- The original evidence supports controlled text agents, not unrestricted, multimodal, or safety-critical autonomy.

## Related-Work Research Lines

> **User-directed revision:** Added a compact map of Section 5's two related-work lines and an analytical interpretation of their underlying research problems.

**Paper-grounded organization.** Section 5 uses two explicit top-level lines. `Language model for reasoning` covers CoT and follow-up prompting, structured-reasoning, and rationale-training methods. `Language model for decision making` covers language models used as policies in browser interaction, dialogue and API calling, and embodied planning. WebGPT, SayCan, and Inner Monologue belong to this second line; SayCan and Inner Monologue are not additional top-level routes.

**Analytical boundary:** This is best read as a ReAct-centered organization of related work, not as a comprehensive taxonomy of all agent research.

| Line                | Paper-grounded focus                                                                                                                                                                                                          | Analytical interpretation — not stated as a formal taxonomy by the paper                                                                                                                                                                                                                                                                                     |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Reasoning**       | Produce intermediate reasoning procedures rather than map a problem directly to its answer. Earlier methods mainly reason over a fixed input and the model's internal knowledge.                                              | The deeper problem is how to make difficult internal computation manageable: select relevant information, decompose a problem, create intermediate representations, maintain working memory, combine partial conclusions, and check or revise them. A reasoning step primarily changes the model's textual or computational context, not the external world. |
| **Decision making** | Use a language model as a policy that selects actions in interactive environments. Prior systems include browser navigation, API decisions, robotic action planning, affordance grounding, and injected environment feedback. | The deeper problem is closed-loop control under partial observability and temporal consequences: infer a belief state from history and observations, ground language in valid and feasible actions, obtain useful feedback, track progress, consider future effects, and recover when an action or plan fails.                                               |

**ReAct as the bridge.** The paper explicitly positions ReAct as integrating reasoning with actions and their observations in one stream. **Analytical interpretation:** Thought updates the agent's internal working state and plan; Action queries or changes the external environment; Observation updates the external evidence available to the next decision. ReAct therefore couples two kinds of state update: reasoning helps determine what to do, while interaction changes what can be known and reasoned about next.

## Appendix Guide

> **User-directed revision:** Added a compact map of Appendices A–E, their most valuable evidence and limitations, and a prioritized reading path.

The appendices are unusually substantial: they contain the prompts, implementation details, complete trajectories, and failure examples needed to interpret the compact claims in the main text.

| Appendix | What it contains | What is worth attention |
| --- | --- | --- |
| **A — Additional Results** | GPT-3 experiments, an up-to-date HotpotQA example with a stale benchmark label, and a human-in-the-loop ALFWorld thought edit. | A.1 gives limited cross-model evidence; A.2 shows that live retrieval can produce a defensible answer that static evaluation marks wrong; A.3 directly illustrates natural-language control, but only through one qualitative intervention. |
| **B — Experiment Details** | HotpotQA fine-tuning schedules and the construction of the restricted ReAct-IM ablation. | B.1 clarifies that ReAct/Act and Standard/CoT do not use identical training-step schedules. B.2 shows that merely adding frequent Thought text is insufficient when it cannot recognize completed subgoals, choose the next subgoal, or invoke useful commonsense. |
| **C — Prompts** | Full prompts for HotpotQA, FEVER, WebShop, and ALFWorld, including Standard, CoT, Act, ReAct, and ReAct-IM variants where applicable. | This is the best evidence for how ReAct is actually elicited. The prompts define or expose task-specific action interfaces but do not provide one cross-task checklist for Thought; task-specific demonstrations carry most guidance about how and when to reason. They also reveal the dense reasoning schedule for knowledge tasks and sparse reasoning for long-horizon decision tasks. |
| **D — Trajectories** | Generated FEVER, ALFWorld, and WebShop rollouts. D.2 compares ReAct, Act, and ReAct-IM on the same ALFWorld task. | These examples make the mechanism concrete: useful Thoughts preserve subgoal state and direct the next action, while Act can repeat failed commands and an incorrect Thought can insert a false belief into the history and derail later behavior. In some wrappers, `think` is serialized like a special action and receives `OK`; this acknowledgement is not substantive environmental evidence. |
| **E — More Analysis** | Concrete examples behind the manually labeled HotpotQA success and failure categories. | E.1 is the most useful failure-oriented reading: it exposes hallucinated conclusions, reasoning mistakes, uninformative search results, repetitive loops, and answer-label ambiguity rather than presenting only successful trajectories. |

**Recommended path:** Appendix C → Appendix D.2 → Appendix E.1 → Appendix A.3. Read Appendix B.1 when evaluating the fine-tuning claim, and use the remaining examples as targeted references rather than reading every trajectory linearly.

## Relation: Previous Work

- Title: Chain-of-Thought Prompting Elicits Reasoning in Large Language Models
  Authors: Jason Wei; Xuezhi Wang; Dale Schuurmans; Maarten Bosma; Brian Ichter; Fei Xia; Ed Chi; Quoc Le; Denny Zhou.
  URL: https://arxiv.org/abs/2201.11903
  - Type: baseline
  - Status: not-ingested
  - Work summary: Chain-of-thought prompting elicits multi-step natural-language reasoning from few-shot demonstrations without external interaction.
  - Role: Principal reason-only baseline and one half of the strongest hybrid methods.
  - Limitation: Static reliance on internal knowledge can preserve hallucinated premises and propagate reasoning errors.
  - This paper: Adds executable actions and observations while retaining free-form reasoning.
- Title: WebGPT: Browser-assisted question-answering with human feedback
  Authors: Reiichiro Nakano; Jacob Hilton; Suchir Balaji; Jeff Wu; Long Ouyang; Christina Kim; Christopher Hesse; Shantanu Jain; Vineet Kosaraju; William Saunders; Xu Jiang; Karl Cobbe; Tyna Eloundou; Gretchen Krueger; Kevin Button; Matthew Knight; Benjamin Chess; John Schulman.
  URL: https://arxiv.org/abs/2112.09332
  - Type: system
  - Status: not-ingested
  - Work summary: WebGPT trains a browser-assisted question-answering model with demonstrations, reward modeling, and human feedback.
  - Role: Prior language-model interaction system that retrieves evidence from the web.
  - Limitation: It depends on an expensive training pipeline and does not explicitly expose flexible reasoning traces that guide each action.
  - This paper: Uses prompting to combine reasoning and action without training the principal PaLM-540B policy.
- Title: ALFWorld: Aligning Text and Embodied Environments for Interactive Learning
  Authors: Mohit Shridhar; Xingdi Yuan; Marc-Alexandre Côté; Yonatan Bisk; Adam Trischler; Matthew Hausknecht.
  URL: https://arxiv.org/abs/2010.03768
  - Type: benchmark
  - Status: not-ingested
  - Work summary: ALFWorld aligns a text simulator with embodied household tasks and provides the trained BUTLER baseline.
  - Role: Tests long-horizon planning, commonsense exploration, subgoal tracking, and action execution.
  - Limitation: The ReAct paper evaluates the synthetic text environment, not visual embodied execution.
  - This paper: Applies sparse two-shot thought/action trajectories and compares them with Act and BUTLER.

## Relation: Compared With

- Title: Inner Monologue: Embodied Reasoning through Planning with Language Models
  Authors: Wenlong Huang; Fei Xia; Ted Xiao; Harris Chan; Jacky Liang; Pete Florence; Andy Zeng; Jonathan Tompson; Igor Mordatch; Yevgen Chebotar; Pierre Sermanet; Noah Brown; Tomas Jackson; Linda Luu; Sergey Levine; Karol Hausman; Brian Ichter.
  URL: https://arxiv.org/abs/2207.05608
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Inner Monologue feeds success, scene, and human feedback into language-model planners for closed-loop embodied control.
  - Similarity: Both place environment feedback inside a language-model planning loop without requiring principal-policy weight updates.
  - Difference: ReAct permits sparse, free-form internal reasoning across several text domains; its reported ALFWorld comparison uses only a restricted IM-style prompt, not the original robotics system.
  - When to use which: Use Inner Monologue to study embodied robotic feedback; use ReAct as a general text-agent interaction protocol across retrieval and decision tasks.
- Title: Toolformer: Language Models Can Teach Themselves to Use Tools
  Authors: Timo Schick; Jane Dwivedi-Yu; Roberto Dessì; Roberta Raileanu; Maria Lomeli; Eric Hambro; Luke Zettlemoyer; Nicola Cancedda; Thomas Scialom.
  URL: https://proceedings.neurips.cc/paper_files/paper/2023/hash/d842425e4bf79ba039352da0f658a906-Abstract-Conference.html
  - Type: compares-with
  - Status: not-ingested
  - Work summary: Toolformer generates and filters its own API-call supervision, then fine-tunes a model to decide which tool to call and how to use the result.
  - Similarity: Both integrate external tools with language-model generation through textual calls and returned observations.
  - Difference: ReAct chiefly elicits a thought/action loop at prompt time, while Toolformer acquires API-call behavior through self-supervised data construction and parameter updates.
  - When to use which: Use ReAct when a capable frozen model and demonstrations are available; use Toolformer when the model is trainable and tool-use behavior should be internalized.

## Source Notes

- Metadata sources:
  - Official OpenReview identity: https://openreview.net/forum?id=WE_vluYUL-X
  - arXiv record and ICLR camera-ready PDF: https://arxiv.org/abs/2210.03629
  - Author project page: https://react-lm.github.io/
  - Author code repository: https://github.com/ysymyth/ReAct
- Source verification: The complete camera-ready paper was inspected, including all main sections and appendices. Paper-table values were retained instead of mixing them with different subset results currently reported by the code repository. Publication metadata was cross-checked against the official venue identity, arXiv, DBLP, project page, and repository.
- PDF policy: The source PDF was acquired temporarily outside the repository and was not copied into the vault. Only stable legal source URLs are retained.

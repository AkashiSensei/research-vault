# Paper Note Format Contract

Follow this contract when creating or updating a canonical paper note. The goal
is stable output across different AI agents and models.

## File Path

- Resolve content paths and path-qualified Obsidian links from the Git
  repository root, which is also the Obsidian vault root.
- Canonical paper notes live at `paper-vault/papers/{citekey}.md`.
- `citekey` must match `^[a-z][a-z0-9-]*$`.
- Prefer `firstauthorYYYYshorttitle`, for example `wang2025jenga`.

## Frontmatter Rules

- Preserve the frontmatter key order from `paper-note-template.md`.
- Do not omit required keys.
- Use YAML block lists for multi-value fields.
- Use `null` for unknown scalar values.
- Use `[]` for known-empty list values.
- Use lowercase booleans: `true` or `false`.
- Use ISO dates: `YYYY-MM-DD`.
- `type` must be `paper`.

Controlled values:

```yaml
status: draft | ingested | reading | refined | archived
verification_status: verified | partially_verified | unverified
analysis_confidence: high | medium | low
```

Field conventions:

- `authors`: list of display names, one author per item.
- `tags`: lowercase kebab-case strings for broad filtering.
- `topics`, `areas`, `scenarios`, `methods`: human-readable display names.
- `doi`: bare DOI only, without `doi:` or `https://doi.org/`.
- `arxiv`: bare arXiv ID only, without `arXiv:` or URL prefix.
- `local_pdf`: default to `null` for public vault notes. Do not write absolute
  local filesystem paths into committed notes unless the user explicitly keeps a
  private-only vault.
- `analysis_confidence`: use `high` when the paper text or official full page
  was inspected, `medium` when using verified abstract/metadata, and `low` when
  relying on incomplete or secondary information.

## Markdown Source Wrapping

- In generated vault Markdown, use physical line breaks only when they express Markdown structure or a real semantic boundary.
- Keep each prose paragraph on one physical source line and rely on the editor or renderer to soft-wrap it visually. Never insert hard line breaks merely to fit an arbitrary width such as 80 or 100 columns.
- Keep each blockquote paragraph and each single logical list item on one physical source line. Use additional source lines only for separate list items, nested structure, deliberate paragraph breaks, or syntax that requires them.
- Preserve structural line breaks for headings, blank lines between paragraphs, lists, tables, fenced code, and display math.
- When revising an existing note, reflow the paragraphs touched by the task; do not mechanically rewrite unrelated sections solely to normalize historical wrapping.

## Required Section Order

Every canonical paper note must use these headings in this exact order:

```markdown
# {{title}}

## Core Contribution
## Summary
## Problem
## Research Area And Scenario
## Assumptions And Scope
## Core Idea
## Method
## Experiments And Evidence
## Contributions
## Limitations
## Key Takeaways
## Relation: Previous Work
## Relation: Compared With
## Source Notes
```

Do not rename these headings. If a section has no content yet, write
`Not identified from available sources.` or leave a short TODO-style bullet
explaining what is missing.

Do not create a `Questions`, `Open Questions`, or `Reading Questions` section
during ingestion. Reading questions belong to later reading/refinement
workflows, not the initial paper note.

Do not create a `My Understanding` section during ingestion. User
interpretation belongs to later reading/refinement workflows, not the initial
paper note.

## Core Contribution As Immediate Orientation

Place `Core Contribution` immediately before `Summary`. Write one short prose paragraph containing one or two sentences in the note's configured language.

Make it answer two questions at a glance:

- What does the paper principally introduce, change, demonstrate, or establish?
- Why is that contribution immediately significant for the paper's problem or field?

Name the actual contribution rather than writing a generic sentence such as "this paper proposes a new method." Omit detailed pipeline steps, benchmark numbers, relation history, and a list of limitations unless one boundary is essential to avoid misrepresenting the contribution.

Keep the three orientation layers distinct:

- `Core Contribution` is the **what and immediate significance** layer: a fast answer to what the paper mainly does.
- `Summary` is the **whole-paper map**: the motivating problem, progression, major approach, evidence, and important boundary.
- `Core Idea` is the **insight and why/how logic** layer: the observation or reframing that motivates the approach, why the authors expect it to work, and how the central mechanism realizes that insight.

Necessary technical terms may recur across the three sections, but do not reuse a sentence or turn one section into a close paraphrase of another. Do not add a user-directed revision marker when `Core Contribution` is created as part of a new ingest under this default contract.

## User-Directed Revision Markers

When a user explicitly asks an agent to make a substantive content or
organization change to an existing canonical paper note, add one compact,
portable Markdown blockquote describing what changed.

For an English note:

```markdown
> **User-directed revision:** Briefly state what changed at the user's request.
```

For a Chinese note:

```markdown
> **用户指导修订：** 简要说明根据用户要求修改了什么。
```

Place the marker where a top-to-bottom reader will see it before the revised
material:

- If the revision affects a whole section or subsection, put the marker
  immediately below that heading and before the section body.
- If the revision affects only a local paragraph, list, table, or other block,
  put the marker immediately before that block.
- Do not put the marker after the revised material as an end note.

Keep at most one marker at each affected location. If later user requests
change the same location, rewrite the existing marker to merge the changes into
one concise sentence instead of appending or stacking markers. State only what
changed; omit the original conversation, date, identifier, and edit history.
Use the note's configured language.

The marker records revision provenance, not epistemic status. Continue to
distinguish paper-grounded claims, analytical interpretation, and user
interpretation in the revised content itself. Do not add markers for initial
ingestion, read-only review, typo fixes, or formatting-only changes.

## Summary As Pre-Reading Orientation

Use `Summary` to establish an overall mental model before the reader enters the
detailed sections. It is not limited to one to three sentences and should not
be treated as a lightly rewritten abstract or an expanded paraphrase of `Core Contribution`.

Choose the structure that best fits the paper. A summary may use:

- short prose paragraphs;
- bullets or numbered steps;
- a compact textual or Mermaid flow when sequence or dependency matters;
- a small comparison or mapping table;
- restrained `###` subheadings when they materially improve orientation.

Use only the formats that help; do not require every summary to contain all of
them. Keep the section compact relative to the full note and optimize it for
pre-reading comprehension. Explain the motivating question, the paper's
throughline, the core logic connecting its major parts, and why the work
matters. The summary may introduce major modules and their relationships, but
leave module interfaces, detailed experimental results, assumption analysis,
and full caveats to their canonical later sections.

Keep every claim source-grounded and distinguish the paper's claims from
analytical interpretation when necessary. Write the summary in the same
configured language as the rest of the paper note; never infer or impose a
separate summary language. If the caller or intake configuration requests
English paper notes, the summary must also be in English.

## Core Idea As Explanatory Logic

Use `Core Idea` to unpack the intellectual move behind the contribution. Explain the prior limitation, observation, or reframing that makes the approach sensible; why the authors expect the approach to work; and how the central mechanism embodies that reasoning.

Keep this section above the implementation granularity of `Method`: explain the governing logic and decisive mechanism, not every component interface or training step. Do not merely repeat the contribution claim, retell the full `Summary`, or use experimental results as a substitute for the underlying idea.

## Research Positioning vs Applicability

Keep `Research Area And Scenario` and `Assumptions And Scope` separate:

- `Research Area And Scenario` is for classification and positioning: research
  areas, concrete use scenarios, and why the paper belongs in those indexes.
- `Assumptions And Scope` is for applicability: required conditions, hidden
  assumptions, non-goals, ignored effects, and cases where the result weakens.

Do not put `Key constraint`, `Excludes`, or failure-case analysis in
`Research Area And Scenario`. Put those in `Assumptions And Scope`.

## Scope And Assumption Precision

The `Assumptions And Scope` section must be explicit enough to help the reader
judge applicability and impact.

Use bold sparingly. Bold only the critical limiting word or short phrase, not
the label, the whole bullet, or a full sentence. Good targets for bolding
include the exact hardware family, topology feature, workload shape, trust
model, scale boundary, data assumption, or deployment boundary that changes
whether the paper applies.

For each important assumption, use this shape:

```markdown
- Claim: ...
  - Key constraint: ... **specific limiter** ...
  - Why it matters: ...
  - Excludes or weakens: ...
```

Clarify the actual limiter, whatever kind it is:

- Hardware/platform: say whether the limiter is **GPU**, **NVIDIA GPU**,
  **TPU**, **NPU**, a specific runtime, or just accelerator memory/bandwidth.
- Topology: clarify whether "heterogeneous" means **link bandwidth**,
  accelerator model, vendor, memory capacity, placement domain, or tenant
  workload mix.
- Workload: clarify whether the result needs **long context**, multi-turn reuse,
  bursty arrivals, skewed popularity, large batch sizes, or strict tail latency.
- Trust/security: clarify whether tenants, documents, users, or workers are
  assumed **trusted**.
- Data/evaluation: clarify whether the scope is limited by dataset domain,
  benchmark design, language, modality, scale, or simulator assumptions.
- If the assumption is inferred rather than stated, label it under
  `Implied assumptions`.

Avoid vague statements like "works in cloud environments", "assumes GPUs", or
"for long-context tasks" without the limiting reason.

## Component And Model Explanation

The `Method` section must start with one or two paragraphs that explain the
overall method, pipeline, or system design before listing components. The reader
should understand the high-level flow and how the pieces cooperate before seeing
individual component interfaces.

When describing the paper's method, system, model, algorithm, scheduler, policy,
pipeline, dataset construction step, or evaluation component, make the interface
legible near the place where that component is introduced. Do not create a
separate section just to list components unless the paper itself is best
understood that way.

Use this shape:

```markdown
- Component: ...
  - Role: ...
  - Input: ...
  - Output: ...
  - Transformation: ...
  - Minimal example: ...
```

Guidelines:

- `Input` and `Output` are required whenever a key component or model is
  described.
- `Transformation` should explain what changes between input and output.
- `Minimal example` should be included when the paper gives enough information
  to make one accurate; keep it small and illustrative.
- Do not invent exact numbers, thresholds, labels, prompts, or data fields that
  the paper does not support. Use qualitative examples when exact values are
  unavailable.
- If the paper names a model but does not expose its interface clearly, say
  what is known and mark the rest as not identified.
- For papers without explicit components or models, apply the same input/output
  discipline to the main procedure, experimental setup, theorem statement,
  benchmark construction, or analytical framework where useful.

## Experiments And Evidence As A Comparison Map

Prefer a high-level comparison map when the paper develops its claims through
multiple meaningful contrasts: baselines, ablations, variants, hybrid methods,
scaling conditions, training regimes, robustness tests, failure modes, transfer
settings, or human interventions.

Use a compact table or nested bullets. A useful default shape is:

```markdown
- **Method or condition A vs. B → high-level finding.**
  - Selected evidence: One or two representative results or observations.
  - Supports: The conclusion this evidence reasonably supports.
  - Original: Section, table, figure, or appendix location.
```

Apply these rules:

- Keep the section at the level of the paper's argument, not a transcription of
  every benchmark row.
- Include only representative numbers that make the comparison legible; do not
  reproduce complete result tables by default.
- Treat `Supports` as the inference enabled by the evidence, distinct from the
  experimental setup and the observed metric.
- Say “supports” rather than “proves” unless the source supplies a formal proof.
- Give an original-paper location for each comparison when the source is
  available; do not invent a location from abstract-only evidence.
- Preserve mixed or negative results and new failure modes instead of reporting
  only the best score.
- Do not assign a separate causal contribution to each capability when one
  ablation changes several capabilities together.
- Separate controlled quantitative comparisons from qualitative examples, and
  label single cases as qualitative evidence.
- Add a short boundary note for material confounds such as best-of-prompt
  reporting, unmatched training budgets, external routing rules, or missing
  uncertainty estimates.

Do not use the comparison map as an exclusive format. Supplement or replace it
when important evidence is better represented by formal results, proofs,
system profiles, dataset coverage, user studies, longitudinal results, case
studies, negative findings, or other paper-specific evidence.

## Relation Block Format

Use this exact shape for ingested previous-work relations:

```markdown
- Title: Full paper title.
  Authors: Author One; Author Two; Author Three.
  URL: https://...
  Obsidian: [[citekey]]
  - Type: baseline
  - Status: ingested
  - Work summary: ...
  - Role: ...
  - Limitation: ...
  - This paper: ...
```

Use this exact shape for not-ingested previous-work relations:

```markdown
- Title: Full paper title.
  Authors: Author One; Author Two; Author Three.
  URL: https://...
  - Type: baseline
  - Status: not-ingested
  - Work summary: ...
  - Role: ...
  - Limitation: ...
  - This paper: ...
```

For `Status: ingested`, include `Obsidian: [[citekey]]`. For
`Status: not-ingested`, omit the `Obsidian` line entirely.

Relation entries should describe the related work and why it matters. Include
the paper's full title, authors, and a stable internet URL. Do not add DOI,
arXiv, year, or other metadata fields as separate bullets unless they are the
only useful internet link.

Allowed `Type` values:

```text
extends
improves
criticizes
uses
compares-with
baseline
dataset
benchmark
system
survey
```

When a not-ingested target is later ingested, search existing relation blocks by
full title, URL, authors, known aliases, DOI URL, arXiv URL, and normalized
title. After creating the canonical note, update confidently matched relation
blocks from `Status: not-ingested` to `Status: ingested` and add
`Obsidian: [[citekey]]`.

Use this exact shape for ingested comparison relations:

```markdown
- Title: Full paper title.
  Authors: Author One; Author Two; Author Three.
  URL: https://...
  Obsidian: [[citekey]]
  - Type: compares-with
  - Status: ingested
  - Work summary: ...
  - Similarity: ...
  - Difference: ...
  - When to use which: ...
```

For not-ingested comparison entries, use the same shape but omit the `Obsidian`
line and set `Status: not-ingested`.

Keep relation blocks selective. Prefer 2-6 important relations over a full
reference-list dump.

## Index Update Format

Index files may link to paper notes with Obsidian wiki links.

For index files outside `paper-vault/indexes/contexts/`, group entries by the paper note's
ingest creation month. Use the ISO month from frontmatter `created`, formatted
as `YYYY-MM`, as the `##` heading. Create the heading if it does not exist.

For example, inside `paper-vault/indexes/areas/AI Infrastructure.md`:

```markdown
## 2026-05

- [[paper-vault/papers/wang2025jenga]]
  - Title: JENGA: Enhancing LLM Long-Context Fine-tuning with Contextual Token Sparsity
  - Workload domain: Training and adaptation.
  - System layer: Runtime and execution engine; operator and kernel optimization.
  - Why listed here: Co-designs contextual token sparsity, pattern prediction,
    and permutation-free kernels to reduce activation memory and computation.
```

Use a nested entry with three second-level items by default:

1. `Title`: the paper's exact full title.
2. One index-specific perspective field.
3. `Why listed here`: the paper's core idea, method, or contribution explained
   from the viewpoint of the current index.

Allow a fourth second-level item only when it answers a distinct question that
researchers browsing that index are likely to care about. Do not split one idea
across two fields or restate `Why listed here` under another label. Four is the
maximum; explicit course or seminar contexts may use only two fields when an
additional explanation would be redundant.

Choose the perspective field by index type:

- Area: `Subarea`. For a matrix-shaped area such as AI Infrastructure, the
  independently useful `Workload domain` and `System layer` fields may replace
  `Subarea`, producing a four-item entry.
- Scenario: `Scenario focus`.
- Topic: `Topic focus`.
- Method: `Role in paper`; add `Mechanism` as the fourth field when role and
  operation are both important and non-duplicative.
- Dataset or benchmark: `Use in paper`, emphasizing how the data supports
  training, evaluation, analysis, or a claim.
- Venue: `Publication`.
- Explicit reading context: a concise field such as `Course relevance` or
  `Seminar focus`.

Write each field as one compact sentence or phrase. Do not add authors, metrics,
identifiers, or generic summaries merely to fill the structure. The same paper
may need different `Why listed here` text in different indexes because readers
arrive with different questions.

Use broad research-area names that people commonly use in labs, courses, and
reading groups. It is acceptable for one paper to appear in multiple broad areas
and narrower scenarios.

`paper-vault/indexes/contexts/` is an explicit-reading-context area. Do not update
`paper-vault/indexes/contexts/courses.md`, `paper-vault/indexes/contexts/seminars.md`, or similar files
unless the user explicitly provides that context. In `courses.md`, use course
names as `##` headings. In `seminars.md`, use seminar dates as `## YYYY-MM-DD`
headings.

Do not create a reverse `Index Membership` section inside the paper note.
Index files are the source of truth for navigation. Paper frontmatter may keep
semantic `areas`, `scenarios`, `topics`, and `methods`, but those fields should
not be treated as a synchronized list of index backlinks.

## Source Notes Privacy

`Source Notes` should record metadata sources and verification state, not local
machine details. Do not write local PDF paths, local OneDrive/iCloud/Dropbox
paths, absolute filesystem paths, usernames, or other private directory
structure into `Source Notes` or any committed paper note content.

If the user provides a local PDF, it is acceptable to record that the original
source artifact was inspected, but write this generically, for example:

```markdown
- Source verification: inspected local PDF provided by the user.
```

Do not include the local path.

## Final Self-Check

Before reporting completion, verify:

- There is exactly one H1 title.
- All required headings exist in the required order.
- `Core Contribution` appears immediately before `Summary` as one short paragraph of one or two sentences and names the paper's actual principal contribution.
- `Core Contribution`, `Summary`, and `Core Idea` retain distinct what, whole-paper-map, and why/how roles without near-verbatim repetition.
- `Summary` gives a compact pre-reading map of the paper's throughline and core
  logic, uses the note's configured language, and is not forced into a rigid
  layout.
- `Experiments And Evidence` uses a high-level comparison map when appropriate,
  points to original-paper locations, and retains important non-comparative
  evidence in another suitable form.
- Frontmatter values follow the controlled formats.
- Unknown facts are marked as unknown, not guessed.
- Relation entries use allowed relation types.
- Not-ingested relation targets do not contain Obsidian wiki links.
- Newly ingested papers have been matched against old not-ingested relation
  blocks by title, authors, URL, and known identifiers where possible.
- Non-context index entries are grouped under the ingest creation month.
- Context indexes are updated only when the user explicitly names a course,
  seminar, reading group, project, or similar context.
- No reverse `Index Membership` section exists in the paper note.
- No local filesystem path appears in `Source Notes` or committed note content.
- No PDF file was added to the public repository.
- Every substantive user-directed revision marker appears before the affected
  content or directly below its heading, and no location has stacked markers.
- Every touched prose paragraph, blockquote paragraph, and single logical list item is stored on one physical source line and left to the editor or renderer for visual soft wrapping.

---
name: paper-ingest
description: Add or revise a specified academic paper in an Obsidian-style paper vault as a structured, verified Markdown note. Use when the user provides a paper title, DOI, arXiv ID, URL, PDF link, citation, or discovery item and wants it analyzed, added, or substantively updated, including user-directed revisions to an existing ingest.
---

# Paper Ingest

Add one specified paper to the vault as a durable literature note. This skill is
the formal write path for `paper-vault/papers/*.md`; discovery skills may propose papers,
but ingestion creates the canonical paper note.

## Scope

Use this skill when the user provides a specific paper and asks to add it,
analyze it, summarize it, put it into the vault, or substantively revise its
canonical paper note.

Do not use this skill for broad literature search, reading guidance on an
existing note, or vault-wide synthesis unless ingestion or note revision is
explicitly part of the task.

## Core Rules

- Keep the workflow tool-agnostic: use plain files, explicit paths, and
  portable Markdown/YAML conventions rather than client-specific commands.
- Write generated vault Markdown with semantic source line breaks only: keep each prose paragraph on one physical line and let the editor or renderer soft-wrap it. Never hard-wrap prose at a fixed column; follow `references/format-contract.md#markdown-source-wrapping`.
- When online search is needed, prefer AnySearch if that skill/tool is available
  and appropriate. If AnySearch is unavailable, fails, or is not present in the
  current AI environment, fall back to the environment's built-in search or
  other reliable metadata lookup tools.
- Create exactly one canonical paper note per ingested paper.
- Follow `references/format-contract.md` for frontmatter, headings, relation
  blocks, and index update rules.
- Verify bibliographic metadata before treating it as final.
- If metadata cannot be verified, mark it as unverified instead of guessing.
- Do not store copyrighted PDF files in the public repository.
- Link only conceptually important previous work, not every reference.
- Do not create empty placeholder notes for referenced papers that are not being
  ingested. Describe them without Obsidian links until they are ingested.
- Add later-work relationships from the newer paper to the older paper; do not
  broadly backfill old notes unless the user asks.
- Preserve the difference between paper-grounded claims, AI analysis, and user
  interpretation.
- Mark substantive user-directed revisions to existing notes using the compact
  blockquote convention in `references/format-contract.md`.

## Vault Assumptions

Resolve content paths from the Git repository root, which is also the Obsidian
vault root. Resolve reference paths relative to this skill file. Use this layout:

```text
paper-vault/
  papers/
  indexes/
    areas/
    scenarios/
    topics/
    venues/
    methods/
    datasets/
    contexts/
      courses.md
      seminars.md
  inbox/
skills/
```

If the vault does not yet have these directories, create only the directories
needed for the current ingestion.

## Workflow

### 1. Identify The Paper

Accept any of these inputs:

- DOI
- arXiv ID or arXiv URL
- official paper page
- PDF URL
- title
- BibTeX or citation text
- an item from a discovery note

Extract candidate metadata:

- title
- authors
- year
- venue
- DOI
- arXiv ID
- official URL
- PDF URL
- project/code URL, if relevant

### 2. Verify Metadata

Use available sources such as:

- official venue or publisher page
- arXiv page
- DOI resolver or CrossRef
- Semantic Scholar
- OpenAlex
- author/project page
- the PDF first page, when locally available

Search preference:

- Use AnySearch first for web search, DOI/arXiv/title lookup, official page
  discovery, and URL extraction when it is available in the current environment.
- Treat AnySearch as a preferred capability, not a hard dependency of the vault
  or this skill.
- If AnySearch is unavailable, has quota/network errors, or is not supported by
  the current AI tool, use built-in web search, DOI/arXiv/CrossRef/OpenAlex
  lookup, publisher search, or other reliable search tools.
- Regardless of search provider, prefer primary sources over secondary
  summaries when finalizing metadata.

Verification guidance:

- Prefer DOI/arXiv/official pages over secondary summaries.
- Cross-check title, authors, year, and venue.
- If sources disagree, record the disagreement in `verification_notes`.
- If no reliable source is available, set `verification_status: unverified`.
- If only some fields are verified, set `verification_status: partially_verified`.

Suggested values:

```yaml
verification_status: verified | partially_verified | unverified
analysis_confidence: high | medium | low
source_acquired: true | false
source_verified_against_original: true | false
```

Only set `source_verified_against_original: true` when the actual source
artifact or official page was inspected.

### 3. Check For Existing Notes

Before creating a new note, search existing paper notes by:

- DOI
- arXiv ID
- normalized title
- citekey

If a matching note exists, update it only if the user wants ingestion to enrich
the existing note. Avoid creating duplicates.

For a substantive user-directed revision, update the matching canonical note
and follow `User-Directed Revision Markers` in
`references/format-contract.md`. Do not add a marker for read-only review,
initial ingestion, typo fixes, or formatting-only changes.

### 4. Resolve Pending References To This Paper

Before finalizing the citekey, search existing vault notes for relation entries
that may already describe the paper being ingested:

- DOI
- arXiv ID
- exact or normalized title
- known aliases or title variants
- candidate citekeys
- `Status: not-ingested` relation blocks

If an existing `Status: not-ingested` relation clearly identifies the current
paper, use verified metadata to choose the canonical citekey. After creating the
note, update that relation entry by adding `Obsidian: [[{citekey}]]` and setting
`Status: ingested`.

If multiple existing relation entries describe the same paper, choose one
canonical citekey from verified metadata, then add the same Obsidian reference
to each confidently matched relation entry.

After creating the note, update all confidently matched pending relation blocks:

- add `Obsidian: [[{citekey}]]`
- change `Status: not-ingested` to `Status: ingested`
- preserve the title, authors, URL, work summary, relation role, limitation,
  comparison, and interpretation

Do not create stub files for pending references just to make links resolve.

### 5. Generate Citekey And Filename

Use a stable, readable citekey:

```text
firstauthorYYYYshorttitle
```

Examples:

```text
wang2025jenga
hu2022lora
vaswani2017attention
```

Filename should be stable and readable. Prefer:

```text
paper-vault/papers/{citekey}.md
```

If the vault already uses a different naming convention, follow the existing
convention.

### 6. Create The Paper Note

Use the template in `references/paper-note-template.md` and the strict format
contract in `references/format-contract.md`.

Write an initial note that is useful before deep human reading:

- one- or two-sentence core contribution that makes the paper's main contribution legible at a glance
- reader-oriented summary that establishes the paper's throughline and core
  logic before the detailed sections
- research area and use scenario
- assumptions and applicability boundaries, separated from research positioning
- problem
- core idea that explains the key insight, rationale, and mechanism behind the contribution
- method
- experiments/evidence
- key takeaways
- limitations
- relation blocks
- index file updates, without writing reverse index links into the paper note

Write `Core Contribution` before `Summary` as one short paragraph of one or two sentences. State what the paper principally introduces, changes, or establishes and why that matters; leave the full paper map to `Summary` and the underlying insight, rationale, and central mechanism to `Core Idea`.

Treat `Summary` as a compact pre-reading guide, not as a fixed one-to-three
sentence abstract rewrite. Choose the small Markdown structure that best fits
the paper: short paragraphs, bullets, numbered steps, a compact flow, a tiny
table, or a restrained combination of these. It should help the reader grasp
the paper's motivating question, overall progression, core logic, and why the
work matters before encountering module-level detail. It may name major
components and show how they connect, but leave their fuller descriptions,
interfaces, experiments, and caveats to the later sections.

Write `Summary` in the same configured language as the rest of the paper note.
Do not add a separate language rule for this section. For example, when the
calling intake configuration requests English paper notes, write the summary in
English as well.

Keep the summary factual and source-grounded. If the paper text is unavailable,
base the note on verified abstracts and official metadata, and mark analysis
confidence accordingly.

Do not create reading-question sections such as `Questions`, `Open Questions`,
or `Reading Questions` during ingestion. Those questions should be created later
by a reading or refinement workflow.

Do not create `My Understanding` during ingestion. User interpretation should be
added later by a reading or refinement workflow.

The `Method` section must not be only a component list. Start with one or two
paragraphs that explain the overall method, flow, or system design from a higher
level, including how major components cooperate. Component-level input/output
details should come after the reader has that overall map.

When describing key components, models, algorithms, modules, policies, dataset
construction steps, evaluation components, or pipelines, make the interface
explicit at the point of description. Describe what goes in, what comes out, and
how the component changes or transforms the input. Prefer a tiny concrete
example when the paper provides enough information to make one accurate. The
example should be simple enough to teach the mechanism, not a new result
invented by the ingesting agent.

Example of component/interface clarity:

```markdown
- Component: Contextual token selector
  - Input: token representations, task context, and a token budget.
  - Output: a subset or mask of tokens kept for fine-tuning.
  - Transformation: estimates which tokens are useful under the current context
    and drops low-utility tokens before the expensive training step.
  - Minimal example: Given a 32k-token document and a 25% token budget, the
    selector keeps tokens tied to the target answer and removes repeated boilerplate.
```

For `Experiments And Evidence`, first determine whether the paper supports a
useful comparison map across baselines, ablations, variants, settings, or
evidence sources. When it does, organize the section at a high level around:

- what is compared;
- one or two representative results, when useful;
- what the comparison supports;
- where the evidence appears in the original paper.

Use “supports” rather than “proves” unless the source establishes a formal
result. Prefer representative numbers over reproducing complete result tables.
Separate controlled quantitative comparisons from qualitative examples and
state important reporting boundaries or confounds.

Do not force every paper into a comparison map. Supplement or replace it when
important evidence is better expressed as a theorem, proof, system profile,
dataset analysis, user study, case study, negative result, or another
paper-appropriate form. Follow the detailed selection and formatting guidance
in `references/format-contract.md`.

Keep `Research Area And Scenario` separate from `Assumptions And Scope`.
`Research Area And Scenario` should classify and position the paper: areas,
concrete target scenarios, and indexing rationale. Do not put boundary analysis,
failure cases, or `Key constraint` bullets there.

Use `Assumptions And Scope` for applicability analysis. Be especially specific
there. Do not write vague scope statements such as "runs in the cloud" or "uses
accelerators" without explaining which term matters and why. Identify the key
constraint, explain the mechanism behind the constraint, and state what cases
are excluded or weakened. Use bold sparingly to highlight only the critical
limiting word or short phrase, never the whole label or sentence.

Example of the expected reasoning style:

```markdown
- Claim: Requests are served by an accelerator cluster connected by heterogeneous links.
  - Key constraint: **heterogeneous links**, not merely "accelerator cluster".
  - Why it matters: The proposed policy depends on different movement costs
    across local device memory, peer-device links, host memory, or network paths.
  - Excludes: A single-GPU deployment or a uniform-topology cluster where
    topology-aware placement has little effect.
  - Portability: If the method depends on a vendor-specific primitive such as
    NVLink, CUDA, NCCL, XLA, or a particular NPU runtime, name that dependency;
    if it only needs measurable bandwidth/latency differences and migration
    controls, state that the assumption may generalize across hardware families.
```

### 7. Add Relation Blocks

Create relation links only when they help explain the paper.

Allowed relation types:

- `extends`
- `improves`
- `criticizes`
- `uses`
- `compares-with`
- `baseline`
- `dataset`
- `benchmark`
- `system`
- `survey`

For each important related work, prefer this shape:

For an ingested related paper:

```markdown
- Title: Full paper title.
  Authors: Author One; Author Two; Author Three.
  URL: https://...
  Obsidian: [[citekey]]
  - Type: baseline
  - Status: ingested
  - Work summary: What the related work does.
  - Role: What role the related work plays for this paper.
  - Limitation: What limitation this paper identifies, if any.
  - This paper: What this paper changes, adds, or reuses.
```

For a related paper that has not been ingested:

```markdown
- Title: Full paper title.
  Authors: Author One; Author Two; Author Three.
  URL: https://...
  - Type: baseline
  - Status: not-ingested
  - Work summary: What the related work does.
  - Role: What role the related work plays for this paper.
  - Limitation: What limitation this paper identifies, if any.
  - This paper: What this paper changes, adds, or reuses.
```

Only include `Obsidian: [[citekey]]` when the related paper already has a
canonical note. For a related paper that has not been ingested, do not include an
Obsidian link. Use the full title, authors, and a stable internet URL instead.
The entry should be centered on the related work's content and its role in the
current paper, not on DOI, arXiv, or other bibliographic bookkeeping fields.

### 8. Update Indexes

Suggest or update relevant indexes:

- research area/domain
- use scenario
- venue/conference/journal
- topic
- method
- dataset/benchmark
- reading context, when relevant, such as a course, seminar, reading group, or
  project

`paper-vault/indexes/contexts/` is opt-in. Do not update files such as
`paper-vault/indexes/contexts/courses.md` or `paper-vault/indexes/contexts/seminars.md` unless the user
explicitly names a course, seminar, reading group, project, or other reading
context. When updating context indexes, follow the file's local grouping rule:
courses use course names as `##` headings; seminars use dates as `##` headings.

For all other index files, group entries by the paper note's ingest creation
month. Use the ISO month from frontmatter `created`, formatted as `YYYY-MM`, as
the `##` heading. If the note is newly created, set `created` first and use that
month. If an existing note is being updated, keep its original creation month
unless the user asks to reclassify the index history.

Consider multiple simultaneous memberships. A paper may belong to several
research areas at once, such as `LLM`, `AI Infrastructure`, `Cloud Computing`,
`Computer Networks`, `Databases`, `Systems`, `Security`, or `HCI`.

Prefer research-area names people commonly use in labs, reading groups, course
topics, and paper lists. Use scenario subgroups inside broader area indexes when
helpful. For example, an `LLM` index may contain scenarios such as
`Long-Context`, `Fine-Tuning`, `Inference Serving`, `Evaluation`, and
`Agent Systems`; an `AI Infrastructure` index may contain scenarios such as
`Training Systems`, `GPU Memory`, `Scheduling`, `Serving`, and `Observability`.

If areas have containment relationships, it is acceptable to add the same paper
to both broad and narrow indexes when both views are useful. Do not force a
single taxonomy path.

Do not create excessive index entries. Each index entry should add navigational
or analytical value.

Index files are the source of truth for navigation. Do not create or update a
paper-note section that lists reverse links back to indexes. The paper note may
keep semantic frontmatter fields such as `areas`, `scenarios`, `topics`, and
`methods`, but those fields describe the paper itself; they are not a mirror of
which index files currently link to it.

### 9. Report What Changed

End by reporting:

- created or updated paper note
- indexes updated
- verification status
- any uncertain metadata
- any relation targets that do not yet have notes

## Required References

Read before creating a note:

- `references/format-contract.md` for strict formatting rules.
- `references/paper-note-template.md` for the canonical note skeleton.

Do not rename canonical headings unless the vault already has a stronger local
convention and the user explicitly asks to adopt it.

## Final Self-Check

Before reporting completion, check:

- one canonical note exists for the paper
- all required frontmatter keys are present
- required headings are present in order
- `Core Contribution` precedes `Summary`, stays within one or two sentences, and is substantively distinct from both `Summary` and `Core Idea`
- `Experiments And Evidence` uses a comparison map when it clarifies the
  evidence, includes original-paper locations, and preserves important evidence
  that the map cannot cover
- unknown facts are marked as unknown rather than guessed
- relation types come from the allowed set
- not-ingested relation entries do not contain Obsidian wiki links
- pending references to the newly ingested paper were searched and normalized
  when confidently matched
- non-context index entries are grouped under the ingest creation month
- no paper-note reverse index section was created
- no local filesystem path, including local PDF paths, was written into
  `Source Notes` or other committed note content
- no PDF file was added to the public repository
- user-directed revision markers precede the affected content or sit directly
  below its heading, with no stacked markers at the same location
- every touched prose paragraph, blockquote paragraph, and single logical list item uses one physical source line instead of fixed-column hard wrapping

## Failure Handling

If the paper cannot be identified:

- ask for a DOI, arXiv link, official URL, or PDF
- do not create a formal paper note

If metadata is incomplete but the user still wants a note:

- create a draft note only when there is enough information to identify the work
- set `status: draft`
- set `verification_status: unverified` or `partially_verified`
- include a short `verification_notes` item explaining what is missing

If the note already exists:

- do not duplicate it
- summarize the existing note path
- ask whether to enrich it when the requested action is ambiguous

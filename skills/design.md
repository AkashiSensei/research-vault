# Paper Vault Skills Design

This document sketches the intended AI skill layer for `paper-vault`. The vault
stores paper notes, indexes, and relation blocks as ordinary Markdown files; the
skills define how AI agents should discover papers, add them, refine notes, and
query the knowledge base.

## Core Principle

Keep the workflow staged but not strictly linear:

```text
discover -> ingest -> refine
                   \-> query

paper-intake -> discover -> ingest
```

Each skill has a clear boundary. Discovery should not pollute the formal vault,
ingestion is the only formal paper-note creation path, refinement protects and
deepens the user's understanding, and query reads/synthesizes any ingested note
without requiring that the note has been refined.

## Portability Principle

Paper Vault skills should be tool-agnostic. They may be read by Codex, Claude,
Gemini, Cursor, local scripts, or future agents. Avoid relying on client-specific
commands, hidden state, MCP servers, plugin hooks, or one vendor's skill runtime.

Use plain Markdown instructions, explicit file paths, stable templates, and
portable data formats. External APIs such as arXiv, CrossRef, OpenAlex, Semantic
Scholar, DOI resolvers, and web search are optional capabilities, not required
runtime dependencies.

## `paper-discover`

Find candidate papers for a topic, question, course, seed paper, or research
direction.

Responsibilities:

- Read the current request and explicitly named Markdown briefs, in that
  precedence order.
- Inspect existing paper notes, indexes, and pending relation targets before
  searching. Do not implicitly load intake-owned recommendation history.
- Search papers using web search, arXiv, Semantic Scholar, OpenAlex, CrossRef,
  and citation traversal when available.
- Expand and refine search queries, including synonyms and adjacent terms.
- Follow backward and forward citations selectively, with relevance filters,
  deduplication, and depth limits.
- Verify candidate identity, official pages, legal full-text links, venue
  signals, and changeable impact signals before recommending.
- Evaluate requirement fit, paper quality, originality, applicability, vault
  value, venue recognition, and age-aware influence without treating any one
  signal as decisive.
- Produce a ranked candidate list with recommendation reasons, caveats,
  evidence confidence, and a suggested reading or ingestion order.
- Return a complete result and save it under `paper-vault/inbox/discoveries/` only when the
  user or calling workflow requests persistence.

Non-goals:

- Do not create formal `paper-vault/papers/*.md` notes; selected papers go through
  `paper-ingest`.
- Do not add every discovered paper to indexes.
- Do not expand citation networks without relevance filtering.
- Do not treat CCF/ICORE rank, citation count, or author affiliation as a
  substitute for paper-level assessment.
- Do not recommend already ingested work, explicitly excluded work, or duplicate
  versions within the same result.

## `paper-ingest`

Accept one specified paper and add it to the vault as a structured paper note.

Responsibilities:

- Resolve and verify bibliographic metadata from DOI, arXiv, title, URL, PDF
  URL, or user-provided metadata.
- Generate a stable citekey and paper filename.
- Create the formal paper note from the vault template.
- Summarize the paper's problem, core idea, method, experiments, findings,
  limitations, and takeaways.
- Record source provenance such as official URL, PDF URL, DOI, arXiv ID,
  verification status, and whether a local source was acquired.
- Extract only conceptually important previous-work relations.
- Add typed relation blocks such as `extends`, `improves`, `criticizes`,
  `uses`, `baseline`, `dataset`, and `benchmark`.
- Update relevant indexes after creating the paper note.

Non-goals:

- Do not store copyrighted PDFs in the public repository.
- Do not convert the full reference list into wiki links.
- Do not broadly rewrite old paper notes unless explicitly requested.

## `paper-intake`

Run one configuration-driven discovery and ingestion cycle for a manual or
scheduled recurring workflow.

Responsibilities:

- Read an explicitly named Markdown configuration or the repository default.
- Stop when `paper-vault/papers/` contains unfinished staged, unstaged, or untracked work.
- Own saved candidate history and calculate a lightweight rolling balance from
  up to fourteen recent run results, treating fewer than seven as warm-up.
- Use cross-run balance to choose one shared learning theme, then keep the
  current run internally connected through progression, contrast, or
  complementary evidence.
- Build a self-contained search brief and delegate candidate discovery to
  `paper-discover`.
- Select a connected qualified subset and delegate canonical note creation to
  `paper-ingest`; ingest fewer papers rather than an unrelated set.
- When an external PDF destination is configured, require a verified legal PDF,
  use that exact file as the ingestion source, and deliver it outside the vault
  after the canonical note is created.
- Return a human-readable learning brief that first explains the history- and
  configuration-grounded choice of today's research question, then gives each
  candidate's core contribution, recommendation reason, relationship, and
  status, followed by the ingestion rationale and reading order. Put file and
  Git details last.
- Leave generated candidates, notes, and index edits unstaged and uncommitted
  for human review.

Non-goals:

- Do not create or manage the external schedule.
- Do not make `paper-discover` or `paper-ingest` read intake configuration or
  recurring-run history.
- Do not treat an unselected historical candidate as rejected or permanently
  excluded.
- Do not mix unrelated research directions merely to improve balance statistics
  within one run.
- Do not put source PDFs, symlinks to them, or machine-specific local paths in
  the repository.
- Do not duplicate the discovery, verification, or ingestion rules owned by
  the lower-level skills.

## `paper-refine`

Help the user read, revise, and deepen existing paper notes.

Responsibilities:

- Guide paper reading using lightweight stages such as survey,
  comprehension, and deep reading.
- Incorporate the user's notes, oral explanations, questions, critiques, and
  classroom takeaways into the existing paper note.
- Preserve the distinction between paper claims, AI summaries, and the user's
  own understanding.
- Improve relation blocks when the user identifies meaningful connections.
- Update tags, status, and index membership when the user's understanding
  changes.

Non-goals:

- Do not overwrite the user's notes with generic AI summaries.
- Do not treat uncertain user thoughts as paper-grounded claims.
- Do not create new formal paper notes unless the task becomes ingestion.

## `paper-query`

Search, compare, and synthesize information already present in the vault.

Responsibilities:

- Search paper notes, indexes, relation blocks, and tags.
- Query any ingested paper note, including notes that have not gone through
  `paper-refine`.
- Answer questions about methods, venues, topics, and reading history.
- Compare papers and summarize differences in contributions, assumptions,
  methods, evidence, and limitations.
- Build small literature maps or reading paths from existing notes.
- Use backlinks and relation blocks to explain research lineage.

Non-goals:

- Do not mutate notes or indexes unless the user explicitly asks.
- Do not invent relationships absent from the vault without marking them as
  hypotheses or suggestions.
- Do not replace source verification or ingestion.
- Do not require refined/user-authored notes to answer basic metadata, summary,
  or index questions.

## Shared Rules

- A formal paper note is created only by `paper-ingest`.
- Discovery requirements use this precedence: current user request, explicitly
  named Markdown brief, then skill defaults.
- Skill dependencies are one-way: `paper-intake` composes `paper-discover` and
  `paper-ingest`; neither lower-level skill depends on intake configuration,
  scheduling, or history.
- `paper-query` and `paper-refine` are parallel consumers of ingested notes;
  refinement is optional, not a prerequisite for retrieval.
- A new paper should link to previous work from the new note; old notes do not
  need to be manually backfilled with every later paper.
- Relation links should be semantic, not just bibliographic.
- Public repository contents should be Markdown-first. PDF originals should stay
  local, in Zotero, or in another private storage location. Record stable
  official and legal source URLs in committed notes, not machine-specific local
  paths.
- Metadata and relation formats should remain machine-readable enough for future
  workflows.

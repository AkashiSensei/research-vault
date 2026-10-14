# Paper Vault Skills

This root-level directory contains shared AI-readable workflow instructions.
The current paper workflows maintain the `paper-vault/` module. The skills are
designed to be portable: they use plain
Markdown, stable file conventions, and explicit rules instead of depending on a
specific AI client or runtime.

Content paths are relative to the Git repository root, which is also the
Obsidian vault root. Skill and reference paths are relative to the skill file
that names them. Run workflows from the Git root even when the request concerns
only the paper module.

## Current Contents

- `design.md`: Describes the intended skill workflow:
  `paper-discover -> paper-ingest -> paper-refine`, with `paper-query` able to
  read any ingested note whether or not it has been refined, and
  `paper-intake` composing discovery and ingestion for recurring runs.
- `references.md`: Records external research-skill repositories and patterns
  worth learning from. These are references, not direct dependencies.
- `paper-discover/`: Searches, verifies, deduplicates, evaluates, and ranks
  candidate papers from user requirements or explicitly named Markdown briefs.
  It returns or optionally saves recommendations, but does not load intake
  history, create canonical paper notes, or update indexes.
- `paper-ingest/`: Creates or updates canonical
  paper notes in `paper-vault/papers/*.md` from a specified paper, with metadata
  verification, structured summaries, relation blocks, and index updates.
- `paper-intake/`: Orchestrates one configuration-driven recurring cycle,
  retaining candidate history, balancing up to fourteen recent runs, organizing
  each run as a connected learning batch, and delegating discovery and
  canonical note creation to the two skills above.

## Design Highlights

- Multi-dimensional indexing: one paper can appear in multiple area, scenario,
  topic, method, venue, dataset, or reading-context indexes. The vault does not
  force a single taxonomy path.
- Requirement-driven discovery: the current user request overrides explicitly
  named Markdown briefs. Existing vault contents inform gaps and relationships
  but are not treated as implicit preferences.
- Multi-signal recommendation: paper quality, originality, applicability,
  vault value, venue recognition, and age-aware impact are evaluated
  independently. Venue rank and raw citation count never decide alone.
- International CS coverage: discovery combines broad scholarly indexes, DBLP,
  domain-specific official proceedings, major publisher or society libraries,
  and primary paper pages instead of relying on one search engine.
- Ingest-based deduplication: canonical paper notes and explicit exclusions are
  hard gates. Caller-owned recommendation history is not an implicit skill
  dependency or a permanent exclusion list.
- One-way orchestration: `paper-intake` may read recurring-run history and call
  `paper-discover` and `paper-ingest`; those lower-level skills never depend on
  the intake skill, its configuration, or its history.
- Cross-run balance, within-run coherence: recent ingests guide which shared
  topic to study next, while each candidate set forms a connected mini
  literature map through progression, contrast, or complementary evidence.
- Research-first run reports: recurring intake output first explains why the
  day's research question was selected, then summarizes every candidate and
  ingestion decision as a reading map; file changes and verification details
  appear last.
- External PDF delivery: when configured, intake requires the same verified PDF
  for full-text ingestion and external delivery, preflights the destination
  before search, and keeps PDFs and machine-specific paths out of the vault.
- Controlled index drift: general indexes are grouped by ingest creation month,
  while explicit reading-context indexes such as courses and seminars are only
  updated when the user names that context.
- Applicability-aware notes: each paper should state its research area and use
  scenario, then separately explain the assumptions, constraints, non-goals, and
  cases where the result may weaken.
- Related-work network: important previous or related papers are recorded with
  typed relation blocks, explaining what the current paper extends, improves,
  uses, compares against, or treats as a baseline.
- Content-first related work: related-work entries emphasize the work's title,
  authors, URL, contribution, and role. Obsidian links are added only after that
  paper has been ingested.
- Public-vault hygiene: PDFs and local filesystem paths are kept out of the
  repository, while official URLs and verification notes remain in the paper
  note.

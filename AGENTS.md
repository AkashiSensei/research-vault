# Repository Agent Instructions

## Instruction Scope

- This file contains repository-wide rules.
- Before editing a file, read the applicable instruction files along the path
  from the Git repository root to that file's directory. In each directory,
  use `AGENTS.override.md` when present; otherwise use `AGENTS.md`.
- Nested instructions supplement these rules within their directory subtree.
  More specific instructions take precedence when rules conflict.
- Module instructions: [knowledge](knowledge/AGENTS.md) and
  [paper vault](paper-vault/AGENTS.md).

## Repository Identity And Language

- The GitHub repository is `AkashiSensei/research-vault`.
- Keep the existing local checkout path unchanged when updating repository
  names or Git remote URLs.
- Write repository content in English by default, including notes, reports,
  indexes, documentation, and new templates.
- An explicit user instruction or workflow language configuration overrides
  that default. Do not infer a different artifact language solely from the
  language used in the chat.
- Preserve existing content languages unless translation is requested.

## Repository Semantics

This repository is a research and technical knowledge base. Markdown is the primary artifact,
so commit types must describe the knowledge workflow rather than the file type.
Do not default to `docs(...)` merely because a change edits Markdown.

Use commit subjects in the form `<type>(<scope>): <imperative description>`:

- `ingest(<citekey>)`: add one explicitly selected paper, its canonical note,
  and the directly corresponding index updates.
- `intake(<YYYY-MM-DD>)`: record one configuration-driven intake cycle,
  including its selected papers, discovery/history record, and index updates.
- `framework(<component>)`: change skills, templates, format contracts,
  taxonomies, schemas, or another reusable vault mechanism.
- `refine(<target>)`: substantively revise an existing paper note, relation,
  index explanation, or reading interpretation.
- `automation(<id>)`: change a recurring automation, permission profile, or
  automation-specific environment.
- `meta(<scope>)`: maintain the repository itself without changing the paper
  knowledge or ingestion framework.

Use `docs(...)` only for documentation that is genuinely separate from the
vault's paper knowledge and workflows. Keep subjects concise and do not end
them with a period.

## Repository Layout

- Treat the Git repository root as the Obsidian vault root.
- Keep paper notes, literature indexes, and discovery and intake records in
  `paper-vault/`; follow its module instructions for paths and workflows.
- Keep shared workflow skills in root-level `skills/`. Resolve their reference
  paths relative to the skill file and content paths relative to the Git root.
- Use root-relative Obsidian paths such as
  `[[paper-vault/papers/wang2025jenga]]` for links that include directories.
- Keep reusable technical knowledge in `knowledge/`, grouped by domain;
  follow its module instructions for writing and visual explanations.
- Future research reports belong outside the paper module. Do not create
  content directories until they are needed.

## Commit Boundaries

- Keep framework, schema, taxonomy, and Skill changes separate from newly
  ingested papers unless they are inseparable for correctness.
- Keep one reviewed daily intake coherent: its paper notes, discovery/history
  record, and generated index updates belong in the same `intake(...)` commit.
- Use `ingest(...)` for a standalone paper addition outside a batch intake.
- Stage exact task-relevant paths. Preserve unrelated tracked, untracked, and
  previously staged work.

## Repositories Without `.context`

- Resolve the current Git repository root before looking for project context.
- Use `.context/` only when it exists inside this repository. Never search a
  parent directory's sibling repositories or borrow another checkout's context.
- When `.context/` is absent, skip `ACTIVE_TASK.md` and `ROADMAP.md` Auto-Sync.
- In that mode, `commit-helper` may be used for status, diff, staged-scope, and
  safety checks only. This file is authoritative for commit semantics.

## Git Safety

- History rewrites and force pushes require explicit user authorization.
- A local history rewrite does not authorize pushing rewritten commits.
- Never mix unrelated dirty-worktree content into a commit for convenience.

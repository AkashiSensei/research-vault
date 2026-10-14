# Paper Vault Module Instructions

These instructions apply to `paper-vault/` and its descendants, together with the repository-wide rules in the root `AGENTS.md`.

## Content Paths

- Resolve content paths from the Git repository root, which remains the Obsidian vault root. Run paper workflows from that root.
- Keep canonical paper notes in `paper-vault/papers/`, literature indexes in `paper-vault/indexes/`, and discovery and intake records in `paper-vault/inbox/discoveries/`.
- The default personal intake configuration is `paper-vault/paper-intake-config.md`; keep it ignored by Git. Maintain the shareable example in `paper-vault/paper-intake-config-example.md`.
- Shared workflow skills remain in root-level `skills/`. Resolve their internal reference paths relative to the skill file that names them.

## Workflow and Format Authorities

- Use [paper-discover](../skills/paper-discover/SKILL.md) to search, screen, and rank candidates. Discovery alone does not create canonical notes or update literature indexes.
- Use [paper-ingest](../skills/paper-ingest/SKILL.md) to add a selected paper or substantively revise its canonical note. Its [format contract](../skills/paper-ingest/references/format-contract.md) and [note template](../skills/paper-ingest/references/paper-note-template.md) govern note structure and index updates.
- Use [paper-intake](../skills/paper-intake/SKILL.md) for a configuration-driven recurring cycle. It owns configuration loading, intake history, and coordination of discovery and ingestion.
- Keep detailed schemas and procedures in those skills and references rather than duplicating them here. Preserve the applicable workflow's verification, write, and review boundaries.

## Paper Content and References

- Keep one canonical note per paper. Preserve stable citekeys and check existing notes before adding another representation of the same work.
- Distinguish paper-grounded claims, analytical interpretation, and user-directed revisions according to the format contract.
- Add Obsidian links only for papers with existing canonical notes. For other related work, retain verified bibliographic information and source URLs without creating placeholder notes.
- Keep PDFs, extracted full-text working files, and machine-specific local paths out of tracked vault content. Follow the intake workflow's external PDF delivery rules when that output is configured.
- Keep reusable concept explanations in `knowledge/`; paper notes remain the detailed record of individual works. Cross-link when useful.

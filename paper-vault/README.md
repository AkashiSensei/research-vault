# Paper Vault

The paper module of this research and technical knowledge base. It contains
canonical paper notes, literature indexes, and discovery and intake records.
Open the parent repository root as the Obsidian vault, rather than this module
alone, so cross-module links resolve consistently.

All content paths below are relative to the Git repository root. Shared workflow
instructions live in [skills/](../skills/README.md).

Implemented workflows:

- `paper-discover`: search, verify, deduplicate, and rank papers from user
  requirements or explicitly named Markdown briefs.
- `paper-ingest`: turn a selected paper into a canonical note and update
  indexes.
- `paper-intake`: run one configuration-driven recurring intake cycle by
  composing `paper-discover` and `paper-ingest`, while retaining candidate
  history, balancing coverage across recent runs, keeping each run internally
  coherent, and leaving all generated notes uncommitted.

Recurring intake configuration:

- `paper-vault/paper-intake-config-example.md`: version-controlled example for researchers.
- `paper-vault/paper-intake-config.md`: ignored local preferences and per-run settings.

Create the local configuration with:

```sh
cp paper-vault/paper-intake-config-example.md paper-vault/paper-intake-config.md
```

A scheduler only decides when to invoke `paper-intake`; the skill owns one run's
workflow. For example:

```text
Use $paper-intake with paper-vault/paper-intake-config.md to run one intake cycle in this
repository. Leave all generated files unstaged and uncommitted.
```

The scheduler itself is intentionally not stored or managed by this repository.

When the intake configuration names an external PDF destination,
`paper-intake` uses the verified PDF as its ingestion source and delivers a copy
outside the vault. Unattended schedulers need scoped write permission to that
destination. PDFs and machine-specific local paths are never added to the
repository.

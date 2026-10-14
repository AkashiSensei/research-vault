# research-vault

A knowledge base for paper notes, research analysis, and technical understanding.
Open the repository root as one Obsidian vault so notes across modules can link
to each other.

Repository content is written in English by default unless the user or an
explicit workflow configuration specifies another language.

## Current Contents

- [Paper vault](paper-vault/README.md): canonical paper notes, literature
  indexes, discovery records, and recurring intake configuration.
- [AI infrastructure knowledge](knowledge/ai-infra/inference.md): working
  notes on inference concepts, techniques, and performance trade-offs.
- [Shared skills](skills/README.md): AI-assisted workflows for maintaining the
  knowledge base.

Research reports will be added as a separate module when the first report is ready.

## Paper Workflows

Run paper workflows from the Git repository root. Their content lives under
`paper-vault/`; shared skills remain under `skills/`.

Create the ignored personal intake configuration with:

```sh
cp paper-vault/paper-intake-config-example.md paper-vault/paper-intake-config.md
```

For one intake cycle:

```text
Use $paper-intake with paper-vault/paper-intake-config.md to run one intake
cycle in this repository. Leave all generated files unstaged and uncommitted.
```

See the [paper module guide](paper-vault/README.md) for workflow details.

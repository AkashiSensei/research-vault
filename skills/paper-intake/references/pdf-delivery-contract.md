# PDF Source And External Delivery Contract

This contract applies when the intake configuration gives a non-empty
`已摄入论文的 PDF 保存目录`.

## Ownership And Boundary

- `paper-intake` owns PDF acquisition, source verification, temporary-file
  cleanup, and external delivery.
- `paper-ingest` still owns only canonical Markdown notes and permitted index
  updates. Do not make it depend on this contract, the intake configuration, or
  the external destination.
- Never add a PDF, extracted full text, rendered page, attachment directory, or
  local PDF path to the repository.
- Never write the resolved destination or another machine-specific absolute
  path into intake history, canonical notes, relations, or indexes.

## Eligibility

External delivery turns a verified legal PDF into a hard ingestion
requirement:

- a candidate may remain recommended when only an abstract or full-text HTML is
  available;
- it must not be selected for ingestion unless a legal PDF can be acquired,
  opened, identified, and read at the configured evidence level;
- an HTML page printed to PDF, a preview, a slide deck, supplementary material,
  or a file containing only an abstract is not the paper's PDF original.

Prefer the legal source order defined in `../SKILL.md`. Verify that the acquired
file is genuinely a PDF and that its first page and document metadata agree
with the selected paper's title, authors, DOI, arXiv identifier, or other
stable identity evidence. Treat a mismatch as an eligibility failure.

Use the same verified PDF as both:

1. the full-text evidence inspected for ingestion; and
2. the source copied to the external destination.

Do not claim a PDF was inspected or delivered merely because a URL ends in
`.pdf`.

## Destination Resolution And Preflight

Resolve `~` against the current operating-system user. Accept an absolute path
or a home-relative path beginning with `~/`. Reject an ambiguous relative path
and any destination that resolves inside the repository.

After the clean-`paper-vault/papers/` gate and configuration parsing, but before search,
history creation, or any repository write:

1. resolve and normalize the destination;
2. create the destination directory when it does not exist;
3. prove that the current run can write there by creating and removing only its
   own uniquely named empty probe file;
4. stop without search or repository modifications if any preflight action
   fails.

Scheduled tasks run unattended. Do not assume an interactive approval can be
granted later in the cycle. A preflight failure must name the configured
destination and explain that the scheduler needs scoped write access to it.

The skill must not silently widen its own permissions. Before scheduling this
workflow, configure the active Codex permission profile or
`sandbox_workspace_write.writable_roots` to include only the intended PDF
destination. Prefer that narrow directory grant over full filesystem access.
Permission configuration is an environment prerequisite, not repository state
and not part of an intake run.

## Delivery

Acquire each selected PDF into a unique temporary directory outside the
repository. Keep temporary PDFs, extracted text, and rendered pages out of the
vault.

After `paper-ingest` successfully creates the canonical note, copy the exact
verified PDF to the configured destination. Name it:

```text
<citekey> -- <original title>.pdf
```

Make the title component filesystem-safe: remove path separators and control
characters, collapse repeated whitespace, trim trailing dots and spaces, and
shorten it when necessary to keep the complete filename within common
filesystem limits. Preserve the citekey.

Never silently overwrite a different file:

- if the target exists and its bytes match the verified source, reuse it and
  report that result;
- if the target exists with different bytes, keep both by adding a short
  content-hash suffix to the new filename;
- use a temporary sibling file and an atomic rename when the available tools
  support it.

After successful delivery, remove only temporary artifacts created by the
current run. The configured external PDF is an intended output and must remain.

## State And Reporting

A paper is `ingested` when its canonical note exists. PDF delivery is a required
postcondition of a successful intake cycle when this contract is active.

If delivery fails after note creation:

- preserve the valid note and other completed work;
- keep the intake history `partial`;
- record only the decision-relevant delivery failure in `Future Handoff`,
  without recording an absolute local path;
- report the exact failed destination and affected paper in the task output.

The human-readable run report must list, for each ingested paper:

- the verified legal PDF source URL;
- the external delivered file as a clickable local path when the interface
  supports it;
- whether the file was newly copied or an identical existing copy was reused.

Repository-change reporting must explicitly distinguish retained external PDFs
from repository contents and confirm that no PDF was added to the repository.

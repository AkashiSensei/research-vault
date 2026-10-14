# Paper Intake Configuration Contract

Accept a natural-language Markdown configuration. The default repository path is
`paper-vault/paper-intake-config.md`; callers may explicitly name another path.

## Required Headings

Read these sections:

- `每次运行数量`
- `临时引导`
- `研究偏好`
- `原文与质量`
- `输出`
- `写入`

Section names may be translated when an explicitly supplied configuration is
otherwise unambiguous. Treat `未指定`, `not specified`, empty bullets, and
placeholders as absent values.

## Required Values

Interpret:

- `候选文献数`: positive integer;
- `自动生成论文笔记数`: integer from zero through `候选文献数`;
- `生成论文笔记前必须阅读全文`: unambiguous yes/no;
- `仅找到摘要时`: explicit fallback behavior;
- `最低证据可信度`: high, medium, or low;
- `推荐清单和运行报告语言`: output language for candidates and run report;
- `论文笔记语言`: analytical prose language for canonical notes;
- `保留论文原始标题`: unambiguous yes/no;
- `保存推荐记录`: unambiguous yes/no;
- `更新相关索引`: unambiguous yes/no;
- `已摄入论文的 PDF 保存目录`: `未指定`, an absolute path, or a
  home-relative path beginning with `~/`;
- `没有合格论文时`: explicit fallback behavior.

Reject negative counts, an ingestion count larger than the candidate count, or
ambiguous safety and source settings.

When `保存推荐记录` is enabled, save the compact decision and candidate handoff
defined by `history-contract.md`, not the complete discovery audit. When it is
disabled, run without creating durable intake history. State that future
rolling balance, direction continuity, and carry-over reconsideration will use
only history that already exists and may therefore be incomplete.

When `已摄入论文的 PDF 保存目录` is present, external PDF delivery is enabled
and `references/pdf-delivery-contract.md` applies. The configured directory
must resolve outside the repository. A legal, verified, readable PDF then
becomes a hard eligibility gate for ingestion even when full-text HTML would
otherwise satisfy `生成论文笔记前必须阅读全文`. A paper without such a PDF may
remain recommended but must not be ingested.

Treat `未指定` as external delivery disabled. Do not invent a destination.

## Research Preferences

Treat the research-preference section as free-form instructions. It may include:

- long-term directions and active questions;
- preferred paper roles and contribution types;
- desired classic/frontier mix;
- preferred or avoided venues;
- preferred methods, systems, workloads, or scenarios;
- avoided methods or assumptions;
- time range;
- explicit paper exclusions;
- other ranking and reading-order requirements.

An explicit paper exclusion may be identified by title, DOI, arXiv ID, stable
URL, or existing citekey. It is a hard exclusion. Venue and method avoidance is
hard only when the wording is explicitly restrictive.

Do not infer permanent exclusion from a candidate appearing in history or not
being selected for ingestion.

## Temporary Guidance

Treat temporary guidance as short-lived search and ranking context. It may be a
sentence, paper title, URL, citekey, recently encountered idea, or follow-up
question.

Temporary guidance is soft unless it explicitly uses hard language such as
`必须`, `只要`, `排除`, or `不要`. It remains active until the user edits or
removes it from the local configuration.

Temporary guidance cannot weaken deduplication, source, confidence, write, or
safety gates.

## Defaults And Assumptions

Do not invent missing workflow controls. Conservative assumptions are allowed
only for incomplete research wording when a meaningful direction is still
clear. Record those assumptions in the run report.

Current task instructions may override research direction and preferences, but
must not silently override fixed safety rules. An explicitly named
supplementary brief may refine the research direction; it does not replace
workflow-control values from the intake configuration.

# Discovery Requirements Contract

Use this contract to combine user instructions and explicitly named Markdown
briefs without silently changing the user's intent.

## Contents

- Requirement Sources And Precedence
- Hard Constraints And Soft Preferences
- Normalized Search Brief
- Named Requirement Documents
- Common Preference Interpretations
- Conflict Examples

## Requirement Sources And Precedence

Apply this order:

1. current user message;
2. Markdown files explicitly named in the current request;
3. defaults in `paper-discover`.

A higher source overrides a lower source only on the conflicting point. Preserve
all compatible lower-priority preferences.

Do not scan arbitrary Markdown files and treat their contents as user
preferences. Read only explicitly named requirement documents. Scan `paper-vault/papers/`
and `paper-vault/indexes/` separately as vault state. Do not load recommendation history
unless the caller explicitly supplies it.

Treat `Not specified`, `未指定`, `unset`, `null`, empty bullets, instructions,
comments, and TODO placeholders as absent values.

## Hard Constraints And Soft Preferences

Interpret explicit constraint language:

| Intent | English examples | Chinese examples | Behavior |
|---|---|---|---|
| Hard include | must, only, require | 必须、只要、限定 | Reject candidates that do not match |
| Hard exclude | exclude, never, do not include | 排除、不要、禁止 | Reject matching candidates |
| Soft preference | prefer, ideally, prioritize | 尽量、优先、偏向 | Improve rank; do not reject otherwise strong papers |
| Exploration | include some, consider | 可以包含、兼顾 | Reserve limited candidate coverage |

Do not convert `尽量推荐 CCF-A` into `CCF-A only`, or `偏向系统` into
`exclude all algorithm papers`.

## Normalized Search Brief

Before searching, derive:

```text
Goal:
Active questions:
Desired roles:
Preferred contribution types:
Hard constraints:
Soft preferences:
Exclusions:
Seed works:
Preferred venues or ranks:
Time range:
Access and language constraints:
Set organization: independent recommendations | field map | coherent batch
Shared learning question:
Desired relation patterns:
Recommendation count:
Requirement sources:
Assumptions:
```

Show the brief in compact form in the final result. The brief makes assumptions
and conflicting requirements auditable.

## Named Requirement Documents

Accept free-form Markdown requirement documents. They may contain stable
preferences, temporary interests, explicit exclusions, seed papers, or
project-specific constraints. Do not require the user to learn a schema before
receiving recommendations.

Treat temporary guidance as a soft prioritization signal unless its wording
explicitly creates a hard inclusion or exclusion. It may not weaken
deduplication, verification, source, or quality gates.

Useful requirement-document contents include:

- long-term research directions;
- contribution-type preferences;
- desired mix of foundational, representative, and frontier work;
- venue preferences;
- applicability and access constraints;
- standing exclusions;
- default result count.

Do not duplicate the vault inventory in a requirement document. Existing
ingested papers come from `paper-vault/papers/`. Prior recommendations are caller-owned
context, not an implicit dependency of this skill.

## Common Preference Interpretations

### Systems rather than model algorithms

Prefer papers whose main contribution includes system architecture,
implementation, scheduling, resource management, storage/network/runtime
design, deployment, or systems evaluation. Require evidence such as realistic
workloads, end-to-end results, microbenchmarks, scalability, overhead analysis,
or deployment lessons.

Do not classify a paper as systems solely because it:

- runs on accelerators;
- reports throughput;
- proposes an LLM method with an implementation;
- appears in an AI-infrastructure application.

### Classic or foundational work

Look for intellectual lineage, adoption, durable terminology or mechanisms,
independent follow-up, and continued relevance. Include an older work only when
it helps the user understand later work or remains an important reference point.

### High-ranking venues

Treat a preferred rank as one part of fit. Verify the current ranking source and
the exact publication type. Conference workshops, demos, posters, findings,
short papers, and abstracts do not inherit a full-paper venue rank automatically.

### Recent or frontier work

Use publication date, current research activity, novelty, early follow-up,
artifact quality, and evidence strength. Treat low citation counts as
uninformative when the citation window is short.

### Coherent learning batches

Interpret a request for related, progressive, contrasting, or complementary
papers as a set-level requirement. Define one shared learning question, then
cover it from multiple useful roles or perspectives. For a multi-paper result,
require every recommended paper to connect to at least one other paper and
require the whole set to remain connected.

Do not confuse coherence with redundancy. Prefer relations such as foundation
to advance, approach A versus approach B, system to evaluation, survey to
representative work, or measurement to solution. Return fewer papers rather
than filling the set with unrelated work.

## Conflict Examples

- A named brief says `Prefer systems`; the current request asks for transformer architecture
  foundations. Follow the user request and disclose that it overrides the
  standing systems preference for this run.
- A named brief says `Prefer CCF-A`; a seminal paper appeared outside CCF-A. It may
  still rank highly because the preference is soft, but explain the exception.
- A named course brief requires papers before 2020; the current user message
  asks for work from 2024 onward. Follow the current message.

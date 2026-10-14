# Paper Intake History Contract

Use this contract only for recurring-run history owned by `paper-intake`.
`paper-discover` may still return a detailed result, but intake history is a
compact decision and handoff record rather than a search audit or paper note.

## Contents

- File And Frontmatter
- Required Sections
- Direction Decision
- Learning Goal And Reading Path
- Paper Decisions
- Future Handoff
- Excluded Detail

## File And Frontmatter

Save one record under:

```text
paper-vault/inbox/discoveries/YYYY-MM-DD-{topic-slug}.md
```

Use:

```yaml
---
type: paper-intake-history
status: partial
topic: "..."
research_lanes: ["..."]
intake_run_at: "ISO 8601 timestamp with timezone"
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

When history saving is enabled, create this in-progress record immediately
after forming the direction decision and before the first discovery query.
Change it to `completed` only after the cycle finishes normally. Leave it
`partial` when later work fails. `completed` means that the intake cycle
finished, not that the user finished reading.

## Required Sections

```markdown
# Paper Intake: {topic}

## Direction Decision

## Learning Goal And Reading Path

## Paper Decisions

## Future Handoff
```

Keep these section headings and candidate field labels exactly as shown for
reliable future parsing. Write their prose values in the configured run-report
language.

## Direction Decision

Preserve the substantive decision memo formed before discovery. Write one to
three connected paragraphs, not a keyword list. Explain:

- what the active request, temporary guidance, and stable configuration say;
- what recent ingested history has and has not covered;
- the user's recorded familiarity or learning stage when relevant;
- why this question and mix of paper roles are appropriate now;
- how the intended papers form a progression, contrast, or complementary view;
- which credible alternative was deferred and why, when material.

Do not reconstruct this rationale after seeing the winning papers. If source
availability or discovery evidence forced a change, preserve the original
decision and add a clearly labeled `Decision adjustment` paragraph.

## Learning Goal And Reading Path

Keep this section compact:

- one question the reader should be able to answer after the batch;
- one ordered learning path expressed as paper roles or concepts;
- the number of prior runs inspected and whether the window is warming up.

## Paper Decisions

Keep one compact entry per final candidate:

```markdown
### 1. [Original Paper Title](official-page-url)

- Status: ingested | recommended | selected | skipped
- Role: survey
- Lane: Agent
- Subarea: taxonomy and architecture
- Core: one plain-language sentence about the work itself.
- Why today: one sentence connecting it to the direction decision.
- Relation: its place in the batch and at least one connected paper.
- Handoff: [[paper-vault/papers/citekey]] | reconsider later because ... | skipped because ...
```

The official-page link is required. Add one stable identifier only when the
link alone is insufficient for reliable future deduplication. For an
un-ingested candidate, preserve a verified legal full-text link only when it
materially reduces future reacquisition work.

Distinguish quota deferral from quality, source, confidence, or coherence
failure. Preserve why a strong un-ingested candidate remains worth
reconsidering. Do not reserve a future slot.

If no paper qualifies, write `No qualified recommendation` in this section and
record the decision-relevant failed gate. Include a `skipped` paper only when
it was a serious verified candidate whose failure should affect a future
decision; never copy the rejected pool.

## Future Handoff

Record only information that should affect a later intake decision:

- coverage added by papers whose final status is `ingested`;
- strong un-ingested candidates that may compete again;
- unresolved subareas, contrasts, prerequisites, or follow-up questions;
- repetition to avoid unless guidance changes;
- decision-affecting caveats or uncertainties.

Do not count `recommended` or `selected` papers as completed coverage.

## Excluded Detail

Do not copy authors, affiliations, venue descriptions, work type, discovery
path, citation counts, ranking lookups, relative scores, candidate-pool counts,
source-service inventories, or routine verification logs into intake history.
Keep a detail only when it directly changed the direction, eligibility,
selection, or future handoff. Detailed analysis of ingested work belongs in its
canonical paper note.

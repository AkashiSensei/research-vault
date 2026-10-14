# Discovery Note Contract

Use this format for both the user-facing result and saved discovery notes.
Saved notes provide an optional audit trail for callers that want persistent
recommendation history. `paper-discover` does not read them implicitly.

## Contents

- File Path
- Frontmatter
- Required Sections
- Set Coherence
- Candidate Entry
- Suggested Order
- Search And Exclusion Summary
- Sources And Freshness
- Updating Candidate Status

## File Path

When saving results, use:

```text
paper-vault/inbox/discoveries/YYYY-MM-DD-{topic-slug}.md
```

Use a short lowercase kebab-case slug. If a same-day file already exists, enrich
it only when it represents the same search; otherwise add a meaningful suffix.

## Frontmatter

```yaml
---
type: paper-discovery
status: active
topic: null
requirement_sources: []
recommendation_count: 0
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

Controlled discovery statuses:

```text
active | completed | archived
```

Controlled candidate statuses:

```text
recommended | selected | ingested
```

## Required Sections

```markdown
# Paper Discovery: {topic}

## Search Brief

## Set Coherence

## Recommended Papers

## Suggested Order

## Search And Exclusion Summary

## Sources And Freshness
```

## Set Coherence

Describe how the recommendation set is organized:

```markdown
## Set Coherence

- Mode: coherent batch | field map | independent recommendations
- Shared question: ...
- Coverage strategy: ...
- Connection map:
  1. Paper A -> Paper B — foundation-to-advance; verified citation relation.
  2. Paper B <-> Paper C — competing approaches with different assumptions;
     analytical comparison from inspected sources.
```

For a coherent multi-paper batch, every candidate must appear in the connection
map, every candidate must have at least one edge, and the entire map must be
connected. For a singleton, explain its relation to the shared question or an
ingested vault paper. For another explicitly requested mode, explain its
organization without inventing artificial relationships.

## Candidate Entry

Use this shape:

```markdown
### 1. Full Paper Title

- Status: recommended
- Authors: Author One; Author Two; Author Three
- Year: 2025
- Venue: Full venue name
- Work type: conference full paper
- Official page: https://...
- Full text: https://...
- Identifiers: DOI ...; arXiv ...
- Discovery path: keyword search | backward from [[citekey]] | forward from ...
- Research lane: Agent | AI Infra | another caller-defined lane
- Subarea: planning | platform | operator optimization | another concise subarea
- Paper role: survey | foundational | representative | bridge | frontier
- Paper summary: One or two source-grounded sentences describing the work.
- Set relationship: prerequisite for Paper B | contrasts with Paper C | ...
- Requirement fit: ...
- Venue signal: CCF A, official seventh edition, checked YYYY-MM-DD; ICORE A*,
  checked YYYY-MM-DD.
- Impact signal: 123 citations in OpenAlex, checked YYYY-MM-DD; interpret with
  age and field caveats.
- Quality and innovation: ...
- Applicability: ...
- Vault relationship: ...
- Recommendation reason: ...
- Caveats: ...
- Evidence confidence: high | medium | low
- Relative score: 86/100
```

Rules:

- `Official page` is required.
- `Full text` is required as a field. Use `Unavailable from verified legal
  sources` when no legal full-text link was found.
- `Recommendation reason` must be specific to the current brief.
- `Research lane` and `Subarea` may use caller-defined labels. Keep them concise
  enough for later history summaries.
- `Paper role` may use another concise caller-defined label when the listed
  roles do not fit.
- `Paper summary` describes the work itself; it is not a substitute for the
  brief-specific recommendation reason.
- `Set relationship` names at least one other candidate when coherence is
  required. State the relation type and whether it is verified lineage or an
  analytical comparison. Use plain paper titles, not wiki links, for candidates
  that have not been ingested.
- Omit neither caveats nor confidence. Use `No material caveat identified from
  inspected sources` when appropriate.
- Use `unknown`, not a guessed value.
- Link an existing vault paper only when it has a canonical note.
- Do not create a wiki link for an un-ingested candidate.
- Collapse version families into one entry and list useful alternate identifiers.

## Suggested Order

Give a learning order based on prerequisites, contrasts, and goals, not merely
the relative score.
For example:

```markdown
1. Paper A — establishes the scheduling model used by the later systems.
2. Paper B — introduces the representative production architecture.
3. Paper C — shows the latest improvement and should be read after A and B.
```

## Search And Exclusion Summary

Report compact counts and meaningful exclusions:

```markdown
- Candidate pool: 31
- Removed as already ingested: 3
- Removed by explicit exclusions: 4
- Collapsed version duplicates: 5
- Rejected by hard constraints: 7
- Insufficiently verified: 2
- Final recommendations: 6
```

Name excluded papers only when the exclusion helps explain a trade-off or a
near-miss. Do not dump the full rejected pool.

## Sources And Freshness

List:

- requirement documents read;
- search services and scholarly indexes used;
- venue-ranking editions and official links;
- citation sources and observation date;
- search date and any coverage limitation.

## Updating Candidate Status

When the user makes a decision:

- change `recommended` to `selected` before ingestion;
- change it to `ingested` after a canonical note exists.

If a caller wants a paper permanently excluded, put its title, DOI, arXiv ID,
or citekey in an explicit requirement document rather than encoding that policy
only in candidate status.

Preserve the original recommendation reason so future discovery can understand
why the work was considered.

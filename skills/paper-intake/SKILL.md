---
name: paper-intake
description: Run one configuration-driven paper discovery and ingestion cycle for this paper vault. Use for scheduled or manual recurring literature intake that must read research preferences, retain compact direction-decision and candidate history, balance coverage across recent runs, assemble each run as a coherent learning batch, call paper-discover, call paper-ingest for qualified selections, and leave all generated Markdown uncommitted for human review.
---

# Paper Intake

Orchestrate one safe, repeatable intake cycle. Keep scheduling outside this
skill: a scheduler decides when to invoke it, while this skill decides what one
run does.

## Required Inputs

Read:

1. the configuration contract in `references/config-contract.md`;
2. the intake-history contract in `references/history-contract.md`;
3. the PDF source and external-delivery contract in
   `references/pdf-delivery-contract.md`;
4. the configuration file explicitly named by the caller, or
   `paper-vault/paper-intake-config.md` by default;
5. `../paper-discover/SKILL.md` and every reference it requires;
6. `../paper-ingest/SKILL.md` and every reference it requires before creating a
   canonical paper note.

Resolve configuration and content paths from the Git repository root, which is
also the Obsidian vault root. Paper content lives in `paper-vault/`; shared
skills stay in root-level `skills/`. Resolve sibling skill
and reference paths from the directory containing this skill. If a required
file is absent or unreadable, stop without modifying the repository and report
its path.

## Boundaries

- Own configuration loading, compact recurring-run decision history, rolling
  balance, candidate selection, batch coherence, safety gates, PDF acquisition
  and external delivery when configured, and handoff between discovery and
  ingestion.
- Delegate paper search, verification, deduplication, evaluation, and ranking to
  `paper-discover`.
- Delegate canonical note creation and index updates to `paper-ingest`.
- Never require `paper-discover` or `paper-ingest` to know this skill,
  configuration location, scheduler, or recurring-run history.
- Do not create, update, enable, pause, or delete a scheduled task.
- Run exactly one cycle per invocation.

## Learning Principle

- Balance research lanes, subareas, and paper roles across runs.
- Keep each individual run centered on one shared research question or learning
  objective.
- Build breadth within that shared question through complementary roles such as
  foundation and advance, competing approaches, system and evaluation, survey
  and representative work, or problem measurement and solution.
- Never satisfy historical balance by combining unrelated papers in one run.

## Fixed Safety Rules

- Never stage, commit, push, or publish repository changes.
- Do not create or switch branches, fetch, pull, merge, rebase, reset, clean,
  restore, discard, or stash changes.
- Preserve every file and modification that existed before the run.
- Require `paper-vault/papers/` to be completely clean at the start. Any staged, unstaged,
  or untracked path under `paper-vault/papers/` means an earlier intake or human review is
  unfinished.
- A dirty working tree outside `paper-vault/papers/` is not by itself a blocker. Before
  modifying any discovery note, relation, or index, ensure that target did not
  already have uncommitted changes.
- Use verified legal sources. Never bypass paywalls, authentication, or access
  controls.
- Keep all PDF and full-text working artifacts outside the repository. Retain
  only configured external PDF copies for successfully ingested papers.
- Never write credentials, cookies, private tokens, temporary paths, or
  machine-specific absolute paths into vault files.
- Never weaken hard requirements, exclusions, verification, source, or
  confidence gates merely to fill a quota.

## Workflow

### 1. Enforce The Clean Papers Gate

Before web search or file writes, run the equivalent of:

```text
git status --porcelain --untracked-files=all -- paper-vault/papers/
```

If it returns any entry, stop immediately. Do not search, recommend, ingest, or
modify any file. Report the blocking paths and their Git states.

### 2. Capture Initial State

Record the initial Git status without changing it. Inventory:

- canonical notes under `paper-vault/papers/`;
- relevant relation targets and indexes;
- saved intake histories and standalone discovery notes under
  `paper-vault/inbox/discoveries/`.

Canonical notes are the hard deduplication source. Discovery notes are
optional `paper-discover` audit artifacts. Records with
`type: paper-intake-history` are intake-owned history used for decision
continuity, balance, and status tracking, not a permanent exclusion list.

### 3. Resolve Configuration

Parse the selected Markdown configuration according to
`references/config-contract.md`. Treat `未指定` as absent.

Apply research-direction precedence:

1. current task request;
2. explicitly named supplementary Markdown briefs;
3. temporary guidance in the intake configuration;
4. stable research preferences in the intake configuration;
5. `paper-discover` defaults.

Only the configuration controls quantities, source gates, output languages, and
write behavior. Stop before writing if required values are missing,
contradictory, or ambiguous.

### 4. Preflight External PDF Delivery

When `已摄入论文的 PDF 保存目录` is present, follow
`references/pdf-delivery-contract.md`.

Resolve the configured destination and prove that the current run can create
and remove its own probe file there. Do this before web search, intake-history
creation, or any repository write. Reject a destination inside the repository.
If the preflight fails, stop and explain that the unattended scheduler needs
scoped write access to the configured directory.

When the destination is `未指定`, do not retain a PDF after ingestion and do not
invent an external location.

### 5. Review Recent Runs And Calculate Rolling Balance

Read the most recent fourteen saved intake histories under
`paper-vault/inbox/discoveries/`, ordered by their `intake_run_at` timestamps. Use every
available result when fewer than fourteen exist. When fewer than seven results
exist, label the history as a warm-up window and avoid strong balance claims.
During warm-up, use history balance only as a weak tie-breaker; active guidance,
stable preferences, research fit, and clear vault gaps must remain the main
reasons for choosing a direction.
Treat `type: paper-intake-history` as the current format. Also accept a legacy
note as an intake result when it has `intake_run_at` or a
`Rolling Balance Snapshot`; do not count standalone `paper-discover` notes as
intake runs. For legacy intake notes without `intake_run_at`, fall back to
their creation date and filename order, and report that ordering as
approximate.

For current-format histories, read `Direction Decision`,
`Learning Goal And Reading Path`, `Paper Decisions`, and `Future Handoff`.
Prefer these recorded decisions over reconstructing the prior rationale from
paper notes. Do not reopen or resummarize ingested papers merely to infer why a
past batch was chosen.

Across this run window, summarize candidates whose status is `ingested` by:

- research lane;
- subarea;
- paper role when recorded;
- batch theme when recorded.

Do not count a merely `recommended` or `selected` candidate as a topic already
covered. Do not treat a prior recommendation as rejection or exclusion.
Inspect all recorded batch themes only to detect repetitive recommendation
patterns; do not mistake a recommended theme for completed learning.

Within the same run window, separately collect up to the configured candidate
count of the most recent unique `recommended` or `selected` candidates that
still lack canonical notes and are not explicitly excluded. Preserve their
identifiers, links, summaries, and original recommendation reasons as
carry-over seeds. They receive no reserved result slots and must compete with
newly discovered work.

Use underrepresented lanes, subareas, and paper roles to help choose one
coherent theme for the current run. Research fit, source quality, paper quality,
and active temporary guidance remain primary. Apply balance between runs, not
by mixing unrelated themes within the current run. Do not impose hard quotas or
lower the quality floor to manufacture balance.

### 6. Make The Direction Decision And Delegate Discovery

Before running any discovery query, form a `Direction Decision` in the
configured run-report language. Write one to three connected paragraphs that
explain:

- the active interests and the user's recorded familiarity or learning stage;
- the relevant coverage, gaps, and carry-over options from recent intake
  history and current canonical holdings;
- why this batch theme and learning goal should be studied now;
- why the intended mix of paper roles creates a useful learning progression;
- which credible alternative direction was deferred and why, when material.

This is a pre-search research decision, not a post-hoc summary of the papers
that happened to rank highest. Keep it in run context, include it in the
self-contained brief sent to `paper-discover`, use it to evaluate set-level
fit, and preserve it in intake history. If source availability or discovery
evidence later forces a material change, retain the original decision and
record a clearly labeled adjustment.

When history saving is enabled, create the new intake-history file now, before
the first discovery query, following `references/history-contract.md`. Set
`status: partial`, preserve the direction decision and intended learning path,
and leave `Paper Decisions` pending. This makes the actual pre-search decision
durable even if discovery or ingestion later fails. Verify that the new target
does not conflict with any pre-run modification. When history saving is
disabled, retain the decision only in run context and disclose that it will not
be available to later runs.

Choose and state:

- one `Batch Theme`;
- one `Batch Learning Goal`;
- one or more useful relation patterns for this batch.

Prefer a theme named by active temporary guidance. Otherwise choose a coherent
theme that advances the configured research direction while improving
cross-run balance.

Build a self-contained Markdown search brief from the resolved configuration.
Include the requested candidate count, explicit paper exclusions, active
temporary guidance, stable preferences, relevant vault holdings, and the
rolling-balance summary. After choosing the theme, classify carry-over
candidates by thematic fit. Include only carry-over candidates that plausibly
address the selected theme as active seeds that may be reverified and reranked,
not as required recommendations. Record strong but off-theme carry-overs as
deferred alternatives in the direction decision and future handoff; do not
send them as active seeds merely because they appeared recently. Make batch
coherence a hard set-level requirement:

- every candidate must address the shared batch theme or learning goal;
- when the requested count exceeds one, every candidate must have a meaningful
  relation to at least one other candidate;
- the complete candidate set must form one connected learning map;
- breadth must come from complementary perspectives or roles within the shared
  topic, not from unrelated research directions.

Pass that brief explicitly to `paper-discover`. Do not ask `paper-discover` to
read the intake configuration or discovery history itself.

Require each returned candidate to include:

- verified identity, official page, and legal full-text link when available;
- research lane, subarea, and paper role;
- a one- or two-sentence source-grounded summary of the paper itself;
- its typed relationship to at least one other candidate when the batch
  contains more than one paper;
- recommendation reason, evidence, caveats, and confidence required by
  `paper-discover`.

Exclude:

- works already represented by canonical notes;
- version duplicates within the current result;
- papers listed under the configuration's explicit paper exclusions;
- works that fail another hard requirement or verification gate.

Do not exclude a paper only because it appeared in an earlier discovery note.
A strong un-ingested candidate may reappear after current verification and
ranking.

When candidate-history saving is enabled, update the partial intake history
using `references/history-contract.md` with compact candidate decisions and the
future handoff. Preserve the pre-search `Direction Decision` and learning path.
Use `type: paper-intake-history`, add `intake_run_at` with an ISO 8601 timestamp
and timezone, store the batch theme in `topic`, record one or more primary
`research_lanes`, and keep `status: partial` until the cycle finishes normally.

Do not persist the complete `paper-discover` audit result merely because intake
history is enabled. Keep detailed verification in the current run context and
canonical notes; omit it from recurring history unless a detail changed the
decision or future handoff. Save a full standalone discovery note only when the
current caller explicitly requests that separate artifact.

Do not overwrite a pre-existing same-day history; use a meaningful suffix when
necessary.

### 7. Select For Ingestion

Select a connected subset of no more than the configured ingestion count.
Evaluate the subset as a learning unit rather than choosing each paper
independently. Use:

1. hard eligibility and source gates;
2. discovery rank and paper-level quality;
3. active temporary guidance;
4. explicit progression, contrast, or complementarity between selected papers;
5. reading-order and prerequisite value;
6. rolling balance as a cross-run tie-breaker.

Do not select a candidate until its identity, source availability, and
confidence satisfy the configuration. If one fails, record the reason and
consider the next candidate. If none qualifies, follow the configured
no-qualified-paper behavior without relaxing constraints.

When selecting more than one paper:

- require every selected paper to connect to another selected paper;
- require the selected subset to remain one connected learning path;
- prefer complementary insight over near-duplicate methods or evidence;
- record whether each relation is a verified citation/lineage relation or an
  evidence-backed analytical comparison.

If no coherent pair or larger subset passes all gates, ingest one qualified
paper rather than filling the quota with unrelated work.

### 8. Acquire Sources And Protect Targets

When full text is required, actually inspect a verified PDF or full-text HTML.
A PDF-looking URL is not evidence that the source was acquired or read.

Prefer legal sources in this order:

1. official open-access publisher, venue, or journal copy;
2. arXiv or another recognized preprint record;
3. author or institutional repository copy;
4. another verified legal full-text source.

When external PDF delivery is configured, full-text HTML alone is insufficient
for ingestion. Before handing a paper to `paper-ingest`, acquire its verified
legal PDF into a unique temporary directory outside the repository, validate
the file and paper identity, and use that exact PDF as the inspected source.
Candidates that lack an eligible PDF may remain recommended but cannot be
selected merely to fill the ingestion quota.

Before the first ingestion, identify the expected paper path and every existing
relation or index target for all selected candidates. If any target had
uncommitted changes in the initial state, stop before creating any canonical
note and report the conflict.

When a saved intake history exists, mark each fully qualified candidate
`selected` immediately before handing it to `paper-ingest`.

### 9. Delegate Ingestion And Deliver PDFs

For each qualified selection, run the complete `paper-ingest` workflow:

- create one canonical note with initial `status: ingested`;
- write analytical prose in the configured paper-note language;
- ground the summary, method, evidence, limitations, and applicability in the
  strongest permitted source;
- preserve stable official and legal source URLs, but not the source file;
- update permitted indexes only when enabled by configuration.

When external PDF delivery is configured, after each canonical note is created:

- copy the exact verified and inspected PDF to the configured destination
  according to `references/pdf-delivery-contract.md`;
- do not place the PDF, a symlink to it, or its local path anywhere in the
  repository;
- retain the external copy and remove only current-run temporary artifacts
  after successful delivery.

After a canonical note exists, update the matching saved history entry to
`ingested` and link its canonical note. Leave unselected qualified candidates
as `recommended`, with a compact future-handoff reason. A generated ingest is
an initial source-grounded note, not evidence that the user has finished
reading it.

If external PDF delivery fails after a note was created, preserve that valid
note, keep the intake history `partial`, record a path-free failure handoff, and
report the partial outcome. Do not claim the cycle completed successfully.

### 10. Verify And Report

Report in the configured run-report language. Treat the saved intake history as
a compact decision and future-handoff record, canonical notes as the detailed
record of ingested papers, and the run report as a concise, human-readable
learning brief. Synthesize the evidence instead of merely listing counts,
statuses, file paths, or actions taken.

After successful verification, set a saved intake history to
`status: completed`. Preserve `status: partial` if the run ended after producing
useful history but before completing the cycle.

Use this section order:

#### Today's Research Decision

Lead with the research judgment, not execution status. State:

- the batch theme as a concrete research question;
- the learning goal and the connected reading path;
- why this theme was selected today, explicitly distinguishing the effects of
  temporary guidance, stable configuration, current vault holdings, recent
  ingested history, prior recommendation themes, and carry-over candidates;
- what credible alternative theme or lane was deferred and why, when one
  materially competed with the selected theme.

Do not claim that history drove the choice when the window is empty or still
warming up. Explain the actual evidence and uncertainty.

#### Recommended Reading Map

Present every recommended candidate in ranked order. For each paper include:

- original title linked to its official landing page;
- one plain-language sentence stating the paper's central problem, method, or
  contribution;
- its role in this batch, such as survey, foundation, representative method,
  contrast, bridge, evaluation, or frontier;
- why it is worth recommending for today's learning goal, including relevant
  quality, venue, impact, applicability, and vault-relationship evidence
  without replacing judgment with a score;
- how it connects to at least one other paper in the batch;
- final status: `ingested`, `recommended`, or `skipped`, with a short reason
  when it was not eligible.

Keep entries compact but substantive. A title-only list is not sufficient.

#### Today's Ingestion Choice

For every ingested paper include:

- the local canonical-note link, official page, and legal full-text link;
- when external PDF delivery is enabled, the delivered PDF's clickable local
  path in the task output and whether it was copied or reused;
- its core idea and the most important limitation or applicability boundary;
- why it was selected for ingestion now rather than merely left recommended;
- how the selected papers complement, contrast with, or build on each other;
- the suggested reading order and what question to carry from one paper to the
  next.

Explicitly distinguish quota deferral from quality, source, confidence, or
coherence rejection. When a strong paper remains `recommended` only because of
the configured ingestion limit, say so. When no paper is ingested, explain the
failed gate rather than presenting an empty selection.

#### Evidence And Caveats

Summarize:

- the actual full-text basis used for each ingestion;
- important metadata disagreements, inaccessible sources, unknown signals, or
  confidence limitations;
- the rolling window's run count and warm-up status, if not already clear.

Do not reintroduce routine per-source audit information into intake history or
the run report. Include it only when it materially affects today's decision,
eligibility, or confidence.

#### Repository Changes

Only after the research brief, report:

- intake-history path, if saved;
- paper notes, relations, and indexes created or updated;
- external PDF files delivered, if configured, clearly labeled as outside the
  repository;
- final Git status for touched files;
- confirmation that no PDF was added to the repository and nothing was staged,
  committed, or pushed.

Do not let the file-change summary dominate the report. Link to the saved
intake history for the direction decision and future handoff, and to canonical
notes for detailed ingested-paper analysis.

Do not stage files during verification.

## Failure Handling

- Stop before any search or write when `paper-vault/papers/` is dirty.
- Stop before writes when configuration cannot be interpreted safely.
- Stop before search or repository writes when configured external PDF
  destination preflight fails.
- When a pre-search gate stops the run, report the blocker directly and do not
  fabricate a research decision or recommendation map.
- When discovery yields no qualified recommendation and history saving is
  enabled, preserve the direction decision, the decision-relevant failed gate,
  and a future handoff without copying the rejected candidate pool.
- Return fewer candidates or ingests when quality or access gates cannot be
  met.
- When external delivery is enabled, retain a candidate as recommended rather
  than ingesting it without a verified legal PDF.
- Return fewer candidates or ingests rather than breaking batch coherence.
- Preserve already completed, valid work if a later candidate fails, and report
  the partial outcome precisely.
- Use `unknown` rather than guessing missing bibliographic or quality signals.

## Final Self-Check

Confirm:

- the configuration path was explicit or the documented default;
- `paper-vault/papers/` was clean before discovery;
- the rolling window examined up to fourteen prior intake results and identified
  warm-up state when it contained fewer than seven;
- candidate history informed balance but did not become an implicit exclusion;
- eligible carry-over candidates were reconsidered without reserved slots;
- a substantive direction decision was formed before discovery and preserved
  without post-hoc rewriting;
- balance chose a theme across runs instead of mixing unrelated themes within
  this run;
- `paper-discover` received a self-contained brief;
- only canonical notes and explicit exclusions were hard-deduplicated;
- candidate summaries and original links were recorded;
- the candidate set and any multi-paper ingestion subset each form a connected
  learning path with explicit relationships;
- the human-readable report leads with today's research decision, gives every
  candidate a substantive core and recommendation reason, and explains each
  ingestion choice before listing repository changes;
- saved recurring history follows the compact intake-history contract and does
  not duplicate routine venue, citation, scoring, source-audit, or paper-note
  detail;
- ingestion used inspected source material at the configured evidence level;
- configured PDF delivery was preflighted before search, every ingested paper
  used the same verified PDF that was externally delivered, and every delivered
  path stayed out of vault files;
- every touched file was free of conflicting pre-run changes;
- no PDF was added to the repository, and no staging, commit, push, or scheduler
  mutation occurred.

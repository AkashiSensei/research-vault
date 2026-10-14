---
name: paper-discover
description: Search, screen, deduplicate, and rank academic papers for this paper vault from user requirements or explicitly named Markdown briefs. Use when the user asks for paper recommendations, a reading list, foundational or frontier literature, venue- or method-constrained discovery, citation-neighborhood exploration, or candidates to add through paper-ingest.
---

# Paper Discover

Build a small, verified, prioritized candidate set for later ingestion. Optimize
for usefulness to this vault, not for the largest possible search result.

## Scope

Use this skill to:

- turn user instructions or Markdown requirement documents into a search brief;
- find foundational, representative, recent, or bridge papers;
- prefer or exclude venues, contribution types, methods, or deployment settings;
- expand from ingested seed papers and pending relation targets;
- recommend papers with original links and evidence-backed reasons.

Do not use this skill to create canonical `paper-vault/papers/*.md` notes, perform a full
systematic review, or deeply summarize every candidate. Send user-selected
papers to `paper-ingest`.

## Required References

Resolve content paths from the Git repository root, which is also the Obsidian
vault root. Paper notes, indexes, and saved discovery records live under
`paper-vault/`. Resolve reference paths relative to this skill file.

Read these files before running a discovery:

- `references/requirements-contract.md` for requirement precedence and parsing.
- `references/computer-science-sources.md` for international computer-science
  source routing and minimum search coverage.
- `references/evaluation-rubric.md` for screening, ranking, and source rules.
- `references/discovery-note-contract.md` for the output and optional saved-note
  format.

## Core Rules

- Treat the current user request as the highest-priority requirement.
- Treat words such as `must`, `only`, `exclude`, and `必须` as hard constraints.
  Treat `prefer`, `ideally`, `尽量`, and `偏向` as soft preferences.
- Inspect the vault before searching. Existing holdings are evidence, not
  implicit user preferences.
- Never recommend the same work twice in one result. Suppress works already
  represented by a canonical paper note and works explicitly excluded by the
  current requirements.
- Do not discover or load recommendation history implicitly. If a caller
  supplies prior candidates, treat them as contextual ranking input rather than
  permanent exclusions unless the caller explicitly marks them excluded.
- Collapse preprint, conference, journal, and title variants that represent the
  same work. Prefer the canonical published version while preserving useful
  open-access links.
- Verify that every recommended paper exists. Never invent titles, authors,
  venues, identifiers, links, citation counts, awards, or rankings.
- Give every recommendation an official landing-page link and a full-text link
  when one is legally available. State clearly when full text is unavailable.
- Record the source and observation date for changeable signals such as
  citations and venue rankings.
- Use venue rank, citation count, and author prominence only as signals. Judge
  the paper's actual contribution, evidence, innovation, applicability, and
  value to the vault independently.
- When the brief requests a coherent learning batch, optimize the final set as
  well as each individual paper. Do not fill the requested count with unrelated
  papers.
- Respond in the user's language unless requested otherwise. Write saved
  discovery notes in English by default; an explicit user instruction or
  caller-supplied language configuration overrides that artifact default.

## Workflow

### 1. Resolve Requirements

Read, in descending precedence:

1. the current user request;
2. requirement Markdown files explicitly named by the user;
3. conservative skill defaults.

Normalize them into a brief containing:

- research question or direction;
- desired paper role: foundational, survey, representative, frontier, bridge,
  application, or replication;
- contribution preference, such as systems, algorithms, theory, measurement, or
  deployment;
- hard constraints and soft preferences;
- date, venue, language, access, and reading-budget constraints;
- seed papers, authors, methods, datasets, or scenarios;
- set organization: independent recommendations, field map, or coherent
  learning batch;
- shared learning question and desired relation patterns when applicable;
- requested recommendation count.

If the requirements are usable but incomplete, continue with explicit
assumptions. Ask a blocking question only when no meaningful search direction
can be inferred.

### 2. Build The Vault Inventory

Search:

- `paper-vault/papers/*.md` frontmatter for DOI, arXiv ID, title, citekey, venue, topics,
  areas, scenarios, and methods;
- relation blocks for important `Status: not-ingested` targets;
- `paper-vault/indexes/` for the vault's current coverage.

Create identity keys from DOI, arXiv ID, Semantic Scholar or OpenAlex ID when
available, normalized title, year, and first author. Use these keys throughout
the run.

Do not infer that the user wants more of every topic already in the vault. Use
the inventory to identify relationships, gaps, redundancy, and prerequisites.

### 3. Plan Candidate Coverage

Create a query family rather than one literal search:

- exact concepts and their common synonyms;
- systems, algorithmic, theoretical, or application terminology implied by the
  requested contribution type;
- known method, workload, dataset, and deployment terms;
- venue- or year-specific queries only when requested;
- backward and forward citation paths from highly relevant seed papers.

Unless the request says otherwise, consider a balanced pool:

- foundational or canonical work;
- strong representative work;
- recent frontier work;
- bridge work that connects the request to existing vault papers.

For a coherent learning batch, first define one anchor question. Search for
complementary roles around it, such as:

- survey or foundation followed by representative or frontier work;
- an original method followed by an extension or critique;
- competing approaches with different assumptions or trade-offs;
- a system design paired with measurement, evaluation, or deployment evidence;
- a problem-characterization paper paired with a solution;
- a method paired with an application that exposes its practical boundary.

Keep diversity inside the anchor question. Do not broaden the pool by adding
unrelated topics merely to represent more research areas.

Generate a candidate pool roughly two to four times the requested result count.
Do not keep expanding once additional search paths produce mostly duplicates or
low-fit papers.

### 4. Search Broadly, Then Verify Narrowly

When available, prefer AnySearch for web discovery and URL extraction. Fall
back to built-in web search or other reliable search tools. Use structured
indexes such as Semantic Scholar, OpenAlex, Crossref, arXiv, DBLP, and official
proceedings as complementary sources rather than interchangeable authorities.

For computer-science discovery, route the search through
`references/computer-science-sources.md`. Unless the request is an exact-title
lookup or is explicitly limited to one venue, use at least:

- one broad scholarly discovery index;
- DBLP or another computer-science bibliography;
- one domain-relevant official proceedings, digital-library, or journal source;
- one structured citation source when impact is evaluated.

Do not search every source mechanically. Cover multiple independent channels
that fit the topic, and report unavailable or subscription-only channels.

For a strong seed paper:

- inspect selected backward citations for foundations and prerequisites;
- inspect selected forward citations for improvements and applications;
- follow only citations whose title, abstract, citation context, or role matches
  the brief;
- normally stop at depth two.

Use recommendation services, co-citation, and bibliographic coupling only to
generate candidates. Verify final candidates independently.

### 5. Verify Candidate Identity

For every shortlisted candidate, cross-check as many of these as available:

- title and title variants;
- authors;
- year;
- work type;
- venue and track;
- DOI or arXiv ID;
- official page;
- legal full-text location;
- whether a conference item is a full/regular paper rather than a workshop,
  short paper, demo, poster, or abstract.

Prefer the publisher, venue, DOI resolver, or arXiv record for bibliographic
facts. A failed lookup in one index is not proof that a paper is fabricated.
Mark unresolved facts as unknown and lower evidence confidence.

### 6. Deduplicate And Apply Hard Gates

Remove:

- works already represented by a canonical paper note;
- duplicate versions of the same work;
- works explicitly excluded by the current request or named requirement
  documents;
- candidates that violate hard requirements;
- candidates whose identity or original link cannot be verified;
- retracted or invalidated work, unless the user explicitly asks to study it.

Keep a compact exclusion summary so the user can understand the search without
receiving the full rejected pool.

### 7. Evaluate And Rank

Apply `references/evaluation-rubric.md`. Evaluate each candidate across:

- requirement fit;
- technical and empirical quality;
- originality;
- applicability and boundary match;
- value relative to the existing vault;
- venue and community recognition;
- scholarly or practical influence.

Use the rubric score for relative ordering only. Compare sources within their
field, age, and paper type. Do not penalize a new paper merely for having few
citations, or reward an old paper merely for accumulating many.

Interpret common preferences carefully:

- `Prefer CCF-A`: use the current official CCF directory as a soft venue signal.
  Do not silently turn it into an A-only filter.
- `CCF-A only`: enforce it as a hard constraint and verify the exact venue,
  edition, and full/regular-paper status.
- `Prefer systems`: favor papers where system design, implementation, realistic
  workloads, and end-to-end or microbenchmark evaluation are central; do not
  classify a paper as systems merely because it uses GPUs or appears near AI
  infrastructure.
- `Prefer classic papers`: favor works with a foundational role, sustained
  community uptake, and clear lineage value. Age alone does not make a classic.
- `Prefer frontier papers`: emphasize current relevance, novelty, and credible
  early evidence; explicitly discount the usefulness of raw citation counts.

### 8. Assemble The Recommendation Set

After evaluating individual candidates, assemble the final set according to the
brief's organization requirement.

For a coherent learning batch:

- when the set contains more than one paper, connect every paper to at least one
  other recommended paper;
- require the whole set to form one connected graph;
- favor complementary roles and perspectives over near-duplicate work;
- label relations such as prerequisite, extends, alternative, contrast,
  critique, benchmark, measurement, application, or replication;
- distinguish a verified citation or lineage relation from an analytical
  comparison inferred from the inspected papers;
- derive the reading order from these relations.

Do not invent a direct citation or influence claim. A shared problem with
contrasting assumptions can establish an analytical relation when both sides
are grounded in inspected sources.

Return fewer recommendations when the quality threshold and the requested set
coherence cannot both be satisfied. When the caller explicitly requests
independent recommendations or a broad field map, state that mode and organize
the set accordingly rather than forcing artificial pairwise links.
A singleton is acceptable only when no qualified connected pair exists; relate
it explicitly to the shared learning question or an ingested vault paper.

### 9. Present And Persist Results

Use `references/discovery-note-contract.md`.

Always provide:

- a concise interpretation of the requirements;
- a ranked recommendation list;
- the set's shared question, organization strategy, and connection map;
- an official page and full-text link for each paper;
- a concise research lane, subarea, paper role, and source-grounded summary;
- each paper's relationship to at least one other candidate when set coherence
  is required;
- a specific recommendation reason;
- quality, innovation, applicability, venue, impact, and vault-relationship
  evidence;
- caveats and evidence confidence;
- a suggested reading or ingestion order.

Return the ranked result to the caller. Save it under
`paper-vault/inbox/discoveries/YYYY-MM-DD-{topic-slug}.md` only when the user or calling
workflow requests persistence. A saved note is an audit artifact; this skill
does not load it automatically on later runs.

Do not create paper notes or update indexes during discovery.

### 10. Hand Off Selected Papers

When the user selects candidates:

- mark their discovery status as `selected` when a discovery note exists;
- pass each selected work's verified identifiers and original links to
  `paper-ingest`;
- let `paper-ingest` perform final metadata verification, canonical note
  creation, relation normalization, and index updates.

## Failure Handling

- If hard constraints leave no credible candidates, return no recommendation
  rather than weakening them silently. Suggest which constraint could be relaxed.
- If only weakly verified candidates exist, separate them from recommendations
  as `Needs verification`.
- If citation or ranking data is unavailable, use `unknown`; do not estimate.
- If requirements conflict, follow the highest-precedence source and disclose
  the conflict.
- If the requested field is outside computer science, do not apply CCF or ICORE
  mechanically; use field-appropriate venue and evidence norms.

## Final Self-Check

Before reporting completion, confirm:

- every recommended work has a verified identity and official page;
- full-text access is linked or explicitly marked unavailable;
- no candidate duplicates an ingested work or another item in the same result;
- version families were collapsed correctly;
- hard constraints were enforced and soft preferences were balanced;
- rankings and citation counts include source and date;
- recent papers were not unfairly penalized for low citations;
- the search used multiple independent, domain-appropriate sources and reported
  meaningful coverage gaps;
- venue prestige did not substitute for paper-level assessment;
- every candidate records a concise lane, subarea, role, and paper summary;
- every recommendation explains its relationship to the request and vault;
- any requested coherent batch forms one connected, evidence-backed learning
  map without unrelated filler;
- discovery did not create canonical paper notes or update indexes.

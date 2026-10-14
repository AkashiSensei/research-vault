# Knowledge Module Instructions

These instructions apply to `knowledge/` and its descendants, together with the repository-wide rules in the root `AGENTS.md`.

## Organization and Writing

- Group notes by domain, such as `knowledge/ai-infra/`, and organize concepts within a note by the technical questions or directions they address.
- Organize knowledge notes as a collection of individual concepts and techniques. Each third-level heading should name one concrete point (for example, Padding or Ragged Batching), rather than a broad explanatory topic or a comparison of several concepts. Use higher-level headings for categories; multiple first-level headings are acceptable when needed for larger collections.
- Explain each concept concisely: what it is, how it works, and the assumptions or trade-offs needed to understand it correctly. Add examples when they clarify a mechanism; let the content determine the structure.
- Keep introductions and transitions brief. If an overview helps navigation, use a short unordered list of categories; do not add an extended learning narrative.
- Number second-level headings (`##`) with uppercase Roman numerals (`I.`, `II.`, `III.`, ...). Number third-level headings (`###`) with Arabic numerals (`1.`, `2.`, `3.`, ...), restarting at 1 within each second-level section. Keep the document title (`#`) unnumbered.
- Use selective relationship or evolution diagrams with short explanations before the relevant concepts when useful. Keep individual knowledge entries central, and distinguish historical succession from complementary techniques without forcing every point into a narrative.

## Diagrams and Images

- Use Mermaid when a relationship, process, or evolution is clearer as a diagram. Place each diagram near the relevant discussion and accompany it with explanatory prose.
- Keep diagrams focused on a coherent question. Do not force unrelated concepts into one progression or add a diagram merely to visualize a simple list. A larger overview is useful only when it conveys meaningful relationships.
- Give arrows explicit meanings, such as data flow, dependency, comparison, or historical development. Make clear which meaning applies.
- Add explanatory images when they materially help readers understand layouts, mechanisms, or other visual details. Original illustrations and well-sourced existing figures are both appropriate.
- Store images in the repository near their notes, for example `knowledge/<domain>/assets/<topic>/`, and embed them using relative Markdown paths with descriptive alt text. Prefer editable SVG for suitable technical diagrams.
- Caption images with what to notice and any simplifying assumptions. Identify whether a figure is original, adapted, or reproduced, and cite its source or technical reference as appropriate.
- Inspect generated or modified images after rendering, and check local image paths and note links before delivery.

## Sources and Paper Links

- Support technical claims with primary papers or official documentation where appropriate. Distinguish source claims from explanatory examples and synthesis.
- Link relevant existing paper notes using root-relative Obsidian links, for example `[[paper-vault/papers/yu2022orca|Orca]]`.
- When a paper has no canonical note, link to its original source. Do not create a broken Obsidian link or a placeholder paper note just to supply a reference.

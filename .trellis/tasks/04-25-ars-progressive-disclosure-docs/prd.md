# brainstorm: ARS Progressive Disclosure Docs

## Goal

Create a progressive-disclosure documentation set that helps future AI conversations quickly load the right amount of CDISC Analysis Results Standard (ARS) context without consuming excessive context window. The documentation should explain ARS as a logical model for planned analysis results and TFL-related structures, starting from compact orientation material and expanding into deeper object-level and relationship-level references only when needed.

## What I Already Know

* The project is CDISC Analysis Results Standard (ARS), focused on machine-readable analysis results metadata and data structures.
* The user understands ARS partly as a way to express planned statistical analysis tables, figures, and listings (TFLs) through structured data.
* ARS is primarily a logical model with a relatively complex object graph, not just a rendering or layout format.
* Root-level non-hidden directories, except `notes/`, are original ARS project files. The `notes/` directory contains user-created notes.
* Current user notes include:
  * `notes/ars-capability-checklist.md`: an ARS capability boundary checklist for evaluating TFL semantic expressiveness.
  * `notes/Analysis.md`: a Chinese explanation of the `Analysis` class.
* The README describes ARS goals as supporting ARM-TS, ARD, common safety displays, a logical analysis results metamodel, automation, traceability, and reproducibility.
* The model documentation and generated artifacts exist in `model/`, `project/docs/`, `docs/`, and schema/serialization formats under `project/`.

## Assumptions (Temporary)

* The primary audience is future AI assistants in this repository, with humans as secondary readers.
* The output should live under `notes/` unless the user prefers a different location.
* The documentation should be written mainly in Simplified Chinese, because existing notes and the conversation are Chinese.
* The docs should favor concise summaries, object relationship maps, decision paths, and links to deeper source files over long copied excerpts.
* The task likely does not require code changes, tests, or application behavior changes.

## Open Questions

* Where should the final progressive-disclosure documentation set live?
* How deep should the first MVP go: overview-only, core TFL flow, or object-by-object reference?
* Should the docs be optimized primarily for AI context loading, human onboarding, or both equally?

## Requirements (Evolving)

* Produce a progressive-disclosure documentation set for ARS.
* Minimize context usage in future conversations by separating high-level orientation from deeper reference material.
* Preserve traceability from summary docs to original ARS source files and user notes.
* Optimize the first MVP for an AI loading/retrieval protocol: future AI sessions should know which compact notes to read first, and which original source files to inspect only when necessary.
* Explain the key TFL-related ARS concepts through the logical model, especially `ReportingEvent`, `ListOfContents`, `Output`, `OutputDisplay`, `DisplaySection`, `Analysis`, `AnalysisSet`, `DataSubset`, `GroupingFactor`, `Group`, `AnalysisMethod`, `Operation`, `OperationResult`, `ResultGroup`, `WhereClause`, and `DocumentReference`.
* Keep the task focused on documentation and knowledge packaging, not programming.

## Acceptance Criteria (Evolving)

* [ ] A top-level entry document exists that can be read in a small context budget and tells an AI what to read next.
* [ ] The documentation set separates overview, core flows, object references, and capability boundaries.
* [ ] Each document has a clear intended context budget and use case.
* [ ] The docs link back to relevant original source files or generated model docs instead of duplicating large source content.
* [ ] The docs explain ARS as a logical model and clearly distinguish semantic expression from physical rendering/layout.
* [ ] The docs include a recommended read order for future AI sessions.

## Definition of Done

* Documentation files are created or updated in the agreed location.
* The final structure is listed in this PRD.
* The docs are self-consistent and cross-linked.
* Verification commands are provided with expected observations.
* No Git commit, branch, push, or other Git write operation is performed unless explicitly requested and confirmed.

## Out of Scope (Explicit)

* Implementing ARS parsers, renderers, validators, or conversion utilities.
* Changing original ARS model files, generated docs, schemas, or examples.
* Rewriting official CDISC documentation wholesale.
* Producing a complete Chinese translation of every generated ARS object page.
* Evaluating clinical/statistical correctness beyond ARS model expressiveness.

## Technical Notes

### Initial Files Inspected

* `README.md`: project goals, background, future-state description, official documentation link.
* `model/ars_ldm.md`: model diagram in Mermaid form.
* `notes/ars-capability-checklist.md`: user-created capability boundary checklist.
* `notes/Analysis.md`: user-created explanation of the `Analysis` class.

### Candidate Documentation Shape

* `notes/ars-ai-guide/README.md`: smallest entry point; what ARS is, what it is not, read-next map.
* `notes/ars-ai-guide/00-ai-loading-protocol.md`: read-order and context-budget rules for future AI sessions.
* `notes/ars-ai-guide/01-mental-model.md`: conceptual model and progressive layers.
* `notes/ars-ai-guide/02-tfl-flow.md`: how a TFL-like table is represented across ARS objects.
* `notes/ars-ai-guide/03-source-map.md`: compact source map from question types to original files.
* `notes/ars-ai-guide/04-capability-boundaries.md`: distilled boundaries based on the existing checklist.
* Later optional layer: `notes/ars-ai-guide/object-notes/` for high-value classes such as `ReportingEvent`, `Output`, `OutputDisplay`, `Analysis`, `Operation`, and `WhereClause`.

### Candidate Progressive Disclosure Levels

* Level 0: one-screen orientation for future AI sessions.
* Level 1: core mental model and TFL flow.
* Level 2: object map and relationship reference.
* Level 3: capability-boundary notes and object-specific deep dives.

### Decision (ADR-lite)

**Context**: The user chose the "AI retrieval protocol" MVP. The immediate value is reducing future AI context usage, not producing a complete human tutorial.

**Decision**: First create a compact documentation set under `notes/ars-ai-guide/` centered on load order, context budgets, task routing, and source-map references. Keep object-level explanation shallow in this iteration and link to generated ARS docs for detail.

**Consequences**: Future conversations can begin by reading a small number of notes and only expand into generated model docs when the task needs object details. Some class-level explanations will remain deliberately brief until a later deep-dive task.

# Medusa Agent Wiki

This wiki is a durable navigation and investigation layer for agents working in
this Medusa checkout. It stores business logic, architecture routing, reusable
query strategies, cross-component flow summaries, and stable investigated
answers. It is not a source mirror and should not duplicate inventories that
`codebase-memory-mcp` can regenerate.

## Lookup Order

1. Classify the question: component, flow, query strategy, or prior Q&A
2. Open the smallest relevant page from this wiki
3. Confirm the graph project whose root matches this checkout
4. Use `codebase-memory-mcp` for symbols, routes, relationships, and package
   structure
5. Read source when behavior needs proof or graph/wiki evidence is incomplete
6. Fall back to text search for literals, config, scripts, docs, generated
   files, or graph gaps
7. Update the wiki when the result is reusable and stable

## Routing

* Ownership and entry points: `components/index.md`
  * Deep component notes:
    `components/core-runtime.md`,
    `components/medusa-api.md`,
    `components/commerce-modules.md`,
    `components/infrastructure-modules.md`,
    `components/admin-ui.md`,
    `components/cli-sdk-tooling.md`,
    `components/docs-generated-surfaces.md`
* Reusable graph/search strategies: `queries/index.md`
* Cross-component conceptual flows: `flows/index.md`
  * Workflow/orchestration flow: `flows/workflows-orchestration.md`
* Reusable investigated answers: `qa/index.md`
  * Test and QA strategy: `qa/test-strategy.md`
* Current deep-ingest queue and coverage status: `ingest-status.md`
* Coverage and provenance: `source.md`
* Wiki change history: `log.md`
* New page templates: `templates/`

## When to Update

Update the wiki for repeated questions, durable routing decisions, query
recipes, cross-module flow understanding, source-vs-graph conflicts,
implementation pitfalls, and stable answers. Do not add broad source summaries,
long caller/callee lists, generated module inventories, or details that are
likely to drift.

## Evidence Model

Every durable claim should be easy to classify:

* Verified facts: stable facts checked against current project files or graph
* Graph evidence: `codebase-memory-mcp` output and the query that produced it
* Source evidence: source files inspected when behavior needed proof
* Inference: conclusions drawn from evidence, labeled as such
* Open questions: unknowns or follow-up checks that should not be treated as
  facts

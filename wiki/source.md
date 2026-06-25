# Wiki Source and Trust

## Coverage

This wiki initially covers repository routing, agent workflow, query strategy,
and high-level flow entry points for the Medusa monorepo. It is intentionally
not a full inventory of packages, routes, workflows, or modules.

## Primary Sources

* Repository source code is the source of truth for runtime behavior
* `package.json`, workspace package manifests, `.yarnrc.yml`, `turbo.json`,
  Jest/Vitest config, and `.github/workflows` are the source of truth for
  commands and automation
* `codebase-memory-mcp` is the preferred source for structural code discovery,
  symbols, graph relationships, architecture overview, and reusable query
  strategy

## Migrated Sources

The initial Codex instructions incorporate useful Medusa-specific content from
the removed Claude instruction files:

* `CLAUDE.md`: monorepo structure, commands, testing conventions, code style,
  module service pattern, API route pattern, workflow pattern, error handling,
  and import conventions
* `.claude/agents/*.md` and `.claude/commands/*.md`: the durable idea of
  separating location, implementation analysis, pattern search, research, and
  implementation-plan workflows was migrated; Claude-specific agent frontmatter,
  command behavior, and tool names were not preserved
* `.claude/skills/writing-docs`: docs project routing, generated-directory
  cautions, MDX metadata/sidebar conventions, prose style, and public-docs scope
  were migrated in summarized form
* `.claude/skills/writing-tsdocs`: public API scope, supported custom tags, and
  high-level per-surface routing were migrated in summarized form
* `.claude/skills/writing-releases`: release-note section order, Full Changelog
  requirement, highlight criteria, and breaking-change handling were migrated in
  summarized form
* `.claude/skills/reviewing-prs` and `.claude/skills/triaging-issues`: PR/issue
  review principles, untrusted-input cautions, template routing, and high-value
  review checks were migrated in summarized form; Claude-only JSON schemas and
  deterministic mutation workflows were not preserved

## Trust Rules

When wiki, graph, and source disagree:

1. Verify behavior in source
2. Treat graph evidence as structural assistance, not runtime proof
3. Update the relevant wiki page if the conflict is likely to affect future
   investigations
4. Keep the conflict note concise and tied to the exact source area checked

## Initial Graph State

On 2026-06-24, this checkout was indexed as
`Users-wuchuni-Desktop-system_design-medusa` with `index_repository` in fast mode
and `persistence=true`. A reusable artifact was written under
`.codebase-memory/graph.db.zst`.

## Deep Ingest Coverage

On 2026-06-25, a batch ingest confirmed the graph project root still matched
`/Users/wuchuni/Desktop/system_design/medusa` and `index_status` returned
`ready`. The ingest added durable routing notes for core runtime, workflows,
Admin/Store API, commerce modules, infrastructure modules/providers, admin UI,
CLI/SDK/tooling, test strategy, docs, generated surfaces, and release metadata.

## Reusable Graph Caveats

Batch 1-9 did not find source-vs-graph behavior conflicts, but did find
reusable graph-query caveats:

* Some path-scoped `search_graph(file_pattern=...)` and `query_graph`
  `file_path` filters returned empty or sparse results where name/BM25
  `search_graph` and source reads found the expected code
* Some workflow and step exports are indexed as `Variable` nodes rather than
  only `Function` nodes
* Some class-level traces, such as `TransactionOrchestrator`, were less useful
  than source verification or method-level searches
* Integration-test `is_test` markers were not reliable for detecting all test
  symbols

Treat these as tool-query limitations, not runtime claims.

## Open Questions

* The old Claude skills contained detailed docs and GitHub automation reference
  files. The initial Codex wiki intentionally stores only durable routing and
  high-level constraints. For substantial docs, release, PR review, or issue
  triage work, inspect current source templates and nearby examples.
* `packages/modules/caching` has an open naming-drift question around caching
  provider registration prefixes
* `integration-tests/api` appears legacy or generated from `.txt` files in this
  checkout; verify its runnable path before relying on it for active coverage
* Generated docs source-of-truth commands for resources references and UI
  component spec JSON still need targeted verification before regeneration

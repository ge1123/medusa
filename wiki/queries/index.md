# Query Strategies

Use this page for reusable graph and search recipes. Prefer graph tools for code
structure and relationships, then read source for runtime behavior.

## Project Setup

Recommended first steps:

```text
list_projects
index_status(project="<project whose root_path matches this checkout>")
get_architecture(project="<project>", aspects=["all"])
```

If no project exists for this checkout, index it:

```text
index_repository(repo_path="/Users/wuchuni/Desktop/system_design/medusa", mode="fast", persistence=true)
```

Use `moderate` or `full` only when semantic search or richer similarity edges
are worth the extra indexing cost.

## Effective Patterns

Find exact framework helpers:

```text
search_graph(project="<project>", query="MedusaService InjectManager InjectTransactionManager", limit=20)
```

Find workflow primitives and examples:

```text
search_graph(project="<project>", query="createWorkflow createStep workflow", limit=20)
```

Find route nodes:

```text
search_graph(project="<project>", label="Route", limit=50)
```

Find symbols in a package by name and then verify via source:

```text
search_graph(project="<project>", name_pattern=".*Order.*", file_pattern="packages/modules/order/.*", limit=50)
get_code_snippet(project="<project>", qualified_name="<exact qualified_name>", include_neighbors=true)
```

Find high-level package boundaries:

```text
get_architecture(project="<project>", aspects=["all"])
```

Find docs/API reference source of truth:

```text
Use graph tools for exported TypeScript symbols under packages/.
Use text search for MDX pages, sidebars, generated specs, and docs prose.
```

## Text Search Fallbacks

Use `rg` or file reads for:

* package scripts and workspace declarations in `package.json`
* CI workflow names and job definitions under `.github/workflows`
* literal error messages, event names, config keys, and env vars
* Markdown docs and generated docs artifacts
* MDX metadata, sidebar entries, and cross-project docs links
* changeset files and release-note source commits
* scripts under `scripts`
* graph gaps, ambiguous route nodes, or generated files not represented well in
  the index

## Known Noisy Areas

* Route graph nodes can include regex-like paths or lack precise file paths;
  verify API behavior in `packages/medusa/src/api`
* For Admin/Store route handler counts, `query_graph` with
  `file_path CONTAINS 'packages/medusa/src/api/admin/'` worked during Batch 3,
  while equivalent `STARTS WITH` filters returned no rows
* Broad workflow searches also return dashboard workflow-execution UI symbols;
  filter by file path or inspect qualified names before reading source
* For core-flows, workflow and step exports are commonly `Variable` nodes. Use
  `query_graph` against variable names containing `Workflow` or `Step` when
  `search_graph` misses them
* During Batch 1 and Batch 2, some scoped `search_graph(file_pattern=...)`
  searches returned no results even when exact symbol searches or
  `query_graph` by `file_path` found source-backed symbols
* During Batch 4-6, graph path behavior stayed inconsistent: some
  `query_graph` filters returned sparse or empty results where BM25/name
  `search_graph` and source reads found the expected code. Treat path-filter
  graph misses as a reason to adjust the query, not as absence proof.
* During Batch 7, `search_graph` found CLI/OAS/HTTP type/telemetry/JS SDK
  symbols, while `query_graph` path filters returned empty rows for the same
  areas
* During Batch 8, integration-test path filters and `is_test` markers were not
  reliable. Use qn-pattern or BM25 `search_graph` for test routing discovery.
* Broad architecture output is useful for orientation but not a substitute for
  source verification of runtime behavior

## Source Evidence

* Initial query recipes came from indexing this checkout on 2026-06-24 and
  running `get_architecture`, `search_graph` for routes, workflow primitives,
  and Medusa service decorators

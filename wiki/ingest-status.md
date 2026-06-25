# Wiki Ingest Status

This page tracks the batch queue for deep wiki ingest. It is a coordination
page, not a source inventory.

## Bootstrap State

## Verified Facts

* Current checkout root: `/Users/wuchuni/Desktop/system_design/medusa`
* Graph project: `Users-wuchuni-Desktop-system_design-medusa`
* Graph root path matches this checkout
* `index_status` reported `ready` on 2026-06-25
* Existing wiki entry points before this ingest: `components/index.md`,
  `queries/index.md`, `flows/index.md`, `qa/index.md`, `source.md`, and
  `log.md`

## Graph Evidence

* `list_projects` found `Users-wuchuni-Desktop-system_design-medusa` with
  `root_path=/Users/wuchuni/Desktop/system_design/medusa`
* `index_status(project="Users-wuchuni-Desktop-system_design-medusa")`
  returned `status="ready"`, `nodes=234486`, and `edges=338326`
* `get_architecture(aspects=["all"])` reported dominant TypeScript coverage,
  route nodes, package clusters for `admin`, `core`, `modules`, `medusa`,
  `cli`, `design-system`, and `www`, plus hotspots around workflow, DML,
  context decorators, dashboard helpers, docs automation, and CLI utilities

## Source Evidence

* `AGENTS.md` requires graph-first discovery, source verification for behavior,
  small wiki updates, and source-vs-graph conflict notes when reusable
* `wiki/index.md` defines the lookup order and evidence model
* `wiki/source.md` records the initial graph as a fast-mode index created on
  2026-06-24

## Inference

* No re-index was required for Batch 0 because the graph project exists, its
  root path matches this checkout, and status is `ready`
* Because the graph was initially recorded as fast mode, semantic/similarity
  evidence should be treated cautiously unless a later ingest records a
  moderate or full re-index

## Queue

| Batch | Scope | Status | Wiki target |
| --- | --- | --- | --- |
| 0 | Bootstrap, graph validation, architecture sketch, queue | Complete | `ingest-status.md`, `index.md`, `log.md` |
| 1 | Core runtime, types, utils, modules SDK | Complete | `components/core-runtime.md` |
| 2 | Workflows SDK, core flows, orchestration | Complete | `flows/workflows-orchestration.md` |
| 3 | Main Medusa admin/store API and request handling | Complete | `components/medusa-api.md` |
| 4 | Commerce modules | Complete | `components/commerce-modules.md` |
| 5 | Infrastructure modules and providers | Complete | `components/infrastructure-modules.md` |
| 6 | Admin, design system, UI packages | Complete | `components/admin-ui.md` |
| 7 | CLI, SDK, telemetry, tooling | Complete | `components/cli-sdk-tooling.md` |
| 8 | Tests and QA strategy | Complete | `qa/test-strategy.md` |
| 9 | Docs and generated surfaces | Complete | `components/docs-generated-surfaces.md` |
| 10 | Consolidation, cross-links, open questions | Complete | `index.md`, `source.md`, topic pages |

## Open Questions

* Batch findings may split or rename target pages if a smaller page boundary is
  clearer after source verification
* No source-vs-graph behavior conflicts have been found through Batch 3
* No source-vs-graph behavior conflicts have been found through Batch 6
* Batch 1-6 found reusable graph-query gaps around some path filters, sparse
  path-scoped Cypher results, and class traces; these are recorded in the
  relevant component/flow/query pages
* Batch 5 found a source naming drift/open question around caching provider
  registration prefixes; it needs targeted verification before being treated as
  intentional behavior or a bug
* Batch 7 found an open question around root `release:next` using
  `chgstangeset`; verify reachability before editing release automation
* Batch 8 found that `integration-tests/api` appears legacy or generated from
  `.txt` files; verify its runnable path before relying on it for active API
  coverage
* Batch 9 found generated docs/reference source-of-truth questions for
  resources references and UI component spec JSON
* Batch 10 updated top-level navigation and source/provenance coverage notes

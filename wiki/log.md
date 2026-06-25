# Wiki Change Log

## 2026-06-25

* Started deep wiki ingest queue for the full Medusa monorepo
* Confirmed codebase-memory graph project
  `Users-wuchuni-Desktop-system_design-medusa` matches this checkout and is
  ready
* Added `ingest-status.md` to track batch coverage, graph provenance, open
  questions, and consolidation state
* Integrated Batch 1-3 findings into `components/core-runtime.md`,
  `flows/workflows-orchestration.md`, `components/medusa-api.md`, and graph
  query caveats in `queries/index.md`
* Integrated Batch 4-6 findings into `components/commerce-modules.md`,
  `components/infrastructure-modules.md`, `components/admin-ui.md`, workflow
  engine flow notes, and additional graph-query caveats
* Integrated Batch 7-9 findings into `components/cli-sdk-tooling.md`,
  `qa/test-strategy.md`, `components/docs-generated-surfaces.md`, and
  additional query caveats for tooling, tests, docs, and generated surfaces
* Completed Batch 10 consolidation by updating top-level navigation,
  `source.md` coverage/provenance, and `ingest-status.md`

## 2026-06-24

* Created Codex-first wiki structure for this Medusa checkout
* Added component routing based on root workspace/package layout, package
  scripts, CI/config files, and codebase graph architecture output
* Added reusable graph query guidance for Medusa services, workflows, routes,
  and source verification fallbacks
* Added flow entry points for request lifecycle, auth, workflows, module
  services, events, workflow engines, data sync/indexing, admin UI, and CLI
* Recorded source/trust rules and noted migration from Claude-specific
  instruction files
* Migrated useful docs, TSDoc, release-note, PR review, and issue triage routing
  from `.claude/skills` into Codex-facing instructions without preserving
  Claude-only execution schemas

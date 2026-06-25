# Core Runtime

Use this page to route framework, module loading, shared type/helper, and
module-service investigations.

## Verified Facts

* Core runtime packages covered here:
  * `packages/core/framework`
  * `packages/core/types`
  * `packages/core/utils`
  * `packages/core/modules-sdk`
* `@medusajs/framework` is the runtime/convenience package for app boot,
  config/runtime loaders, HTTP setup, jobs, links, migrations, subscribers,
  telemetry, workflows, and re-exports of core packages
* `@medusajs/types` is a shared contract package; use it for DTO, service, DAL,
  DML, HTTP, workflow, and module SDK types rather than runtime behavior
* `@medusajs/utils` owns shared helpers, errors, DAL/MikroORM utilities, DML,
  feature flags, policies, totals, and module-service decorators/helpers
* `@medusajs/modules-sdk` owns module registration/loading, module instances,
  app/link bootstrap APIs, remote query, and link utilities

## Routing

Start in `packages/core/framework` when the concern is application boot, HTTP,
config, jobs, migrations, runtime wiring, or framework subpath exports.

Start in `packages/core/modules-sdk` when the concern is module discovery,
module loading, module instances, links, joiner config, or remote query.

Start in `packages/core/utils` when the concern is generated module-service
behavior, decorators, DAL/DML, shared errors, or common runtime helpers.

Start in `packages/core/types` only for shared contracts.

## Source Evidence

* `packages/core/framework/src/container.ts` creates the shared framework
  container through `createMedusaContainer()`
* `packages/core/framework/src/medusa-app-loader.ts` merges configured modules
  with default module definitions, prepares shared database/logger resources,
  and delegates module loading/migrations to modules-sdk app APIs
* `packages/core/framework/src/http/express-loader.ts` owns Express runtime
  setup, sessions, cookie parser, static files, logging, and Redis shutdown
  cleanup
* `packages/core/framework/src/jobs/job-loader.ts` wraps scheduled jobs in
  workflow-backed scheduled workflows named from the job config
* `packages/core/utils/src/modules-sdk/medusa-service.ts` dynamically builds
  module-service CRUD methods, applies context/event/manager decorators,
  serializes repository results, maps soft-delete/restore cascades, and emits
  grouped events when an event bus is present
* `packages/core/utils/src/dml/entity-builder.ts` rejects implicit timestamp
  fields before adding DML default properties
* `packages/core/modules-sdk/src/loaders/module-loader.ts` delegates module
  resolutions to `loadModule` and surfaces resolution failures
* `packages/core/modules-sdk/src/medusa-module.ts` maintains module instances,
  aliases, custom links, loading promises, joiner configs, and module
  resolutions
* `packages/core/modules-sdk/src/remote-query/query.ts` implements `Query.graph`
  over `RemoteQuery.query`

## Graph Evidence

* `trace_path(function_name="MedusaService", direction="inbound", mode="calls")`
  shows direct usage across many commerce modules, plugins, workflow engines,
  and provider-adjacent packages
* `trace_path(function_name="loadModule", direction="inbound", mode="calls")`
  shows `moduleLoader` calling `loadModule`, with `MedusaModule.bootstrap_` and
  `bootstrapLink` reaching it through `moduleLoader`

## Query Notes

```text
search_graph(query="medusa app loader module loader remote query MedusaService", limit=30)
search_graph(name_pattern=".*JobLoader.*", limit=20)
search_graph(name_pattern=".*moduleLoader.*", limit=20)
search_graph(name_pattern=".*EntityBuilder.*", limit=20)
trace_path(function_name="MedusaService", direction="inbound", mode="calls", depth=1)
trace_path(function_name="loadModule", direction="inbound", mode="calls", depth=2)
```

## Open Questions

* The `@medusajs/types` and `@medusajs/modules-sdk` package manifests use
  `export` rather than `exports`; this ingest did not determine whether that is
  intentional packaging behavior or historical metadata

## Graph Gaps

* During this ingest, path-scoped `search_graph(file_pattern=...)` searches
  returned no results for some core packages even when exact symbol searches
  found the symbols. Prefer name/BM25 graph searches plus source verification
  in this area.

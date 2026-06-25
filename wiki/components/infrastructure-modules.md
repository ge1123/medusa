# Infrastructure Modules and Providers

Use this page to route provider, event bus, cache, locking, and workflow engine
implementation work.

## Verified Facts

* Provider loading is centralized in
  `packages/core/modules-sdk/src/loaders/module-provider-loader.ts`
* Provider modules must expose `services`; services can validate provider
  options before registration
* Provider services generally require a static `identifier`
* Event bus modules implement the shared event-bus base from
  `packages/core/utils/src/event-bus/index.ts`
* There are two cache surfaces: simple `cache-*` modules and the newer
  provider-based `modules/caching` surface
* `packages/modules/locking` is a provider-dispatch facade
* `workflow-engine-inmemory` and `workflow-engine-redis` implement the
  `Modules.WORKFLOW_ENGINE` storage/scheduler layer used by orchestration

## Routing

* Event bus: start in `packages/modules/event-bus-local` or
  `packages/modules/event-bus-redis`; base contract starts in
  `packages/core/utils/src/event-bus/index.ts`
* Simple cache: start in `packages/modules/cache-inmemory` or
  `packages/modules/cache-redis`
* Provider-based caching: start in `packages/modules/caching`; Redis provider
  lives in `packages/modules/providers/caching-redis`
* Locking: start in `packages/modules/locking`; concrete providers live under
  `packages/modules/providers/locking-*`
* Workflow engine runtime implementations: start in
  `packages/modules/workflow-engine-inmemory` or
  `packages/modules/workflow-engine-redis`
* General provider loading: start in
  `packages/core/modules-sdk/src/loaders/module-provider-loader.ts`

## Source Evidence

* `moduleProviderLoader` and `loadModuleProvider` resolve providers from import
  strings or direct module exports, validate options, and register services by
  custom function or lower-cased class name
* `CacheProviderService.getRegistrationIdentifier` and
  `LockingProviderService.getRegistrationIdentifier` throw invalid-argument
  errors when provider identifiers are missing
* `event-bus-local` uses a process-global `EventEmitter`, in-memory grouped
  events, optional delayed emission, wildcard subscribers, and logs subscriber
  processing errors
* `event-bus-redis` uses BullMQ queues/workers plus Redis staging lists for
  grouped events; lifecycle hooks close workers, queues, and Redis connections
* Simple cache modules implement `ICacheService` with `get`, `set`,
  `invalidate`, and `clear`
* `modules/caching` plus `providers/caching-redis` implement provider fan-out,
  key/tag lookup, fallback on provider errors, tracing hooks, and
  auto-invalidation semantics
* `CachingModuleService.performCacheSet` fires provider `set` calls without
  awaiting them, so writes are best-effort from the caller's perspective
* Redis caching provider handles Redis connection errors by returning `null` or
  logging and returning; it also compresses larger values and stores tag
  metadata
* In-memory locks are process-local; Redis locks use Lua commands, owner IDs,
  optional TTL, and backoff; Postgres locks combine advisory locks with a
  `locking` table for explicit acquire/release flows
* Workflow engine storage owns retries, step/transaction timeouts, scheduled
  workflow execution, checkpoints, cleanup, and race handling

## Graph Evidence

* `search_graph(name_pattern=".*Abstract.*Provider.*")` shows abstract provider
  bases in `packages/core/utils` for analytics, auth, file, fulfillment,
  notification, and payment

## Runtime Assumptions

* Redis-backed infrastructure needs explicit startup/shutdown connection
  management
* In-memory implementations are process-local and should not be treated as
  distributed coordination mechanisms
* Redis workflow engine supports worker/data-loader split behavior through
  `dataLoaderOnly`
* Provider-based caching writes can be asynchronous and best-effort from the
  module service caller's perspective

## Test Strategy

Use package-local Jest and integration scripts. Prefer provider unit tests for
provider contract behavior and integration tests for Redis, Postgres, BullMQ,
database coordination, race handling, and retry behavior.

## Open Questions

* `packages/modules/caching/src/types/index.ts` defines
  `CachingProviderRegistrationPrefix = "lp_"`, while `CacheProviderService`
  injects keys shaped like `cp_${string}`. Source behavior uses the exported
  prefix, but this naming drift needs targeted verification before being
  treated as intentional or erroneous.
* Redis event bus retry/completed-subscriber behavior was only partially
  inspected in Batch 5
* Provider categories under `packages/modules/providers` were not exhaustively
  inspected to avoid a brittle inventory

## Graph Gaps

* Path-scoped `query_graph` over infrastructure packages returned sparse
  results for some areas where BM25/name searches and source reads found the
  expected classes and methods
* `query_graph` for `INHERITS` under infrastructure paths returned no rows even
  though source shows classes extending or implementing framework contracts

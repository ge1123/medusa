# Main Medusa API Surface

Use this page to route Admin and Store API route, middleware, and request-state
investigations.

## Verified Facts

* Admin API route handlers live under `packages/medusa/src/api/admin`
* Store API route handlers live under `packages/medusa/src/api/store`
* API handlers are exported from `route.ts` files as uppercase HTTP verb
  functions
* Current graph/source checks found Admin and Store route handlers for `GET`,
  `POST`, and `DELETE`; no `PUT` or `PATCH` handlers were found in these two
  surfaces during Batch 3
* `packages/medusa/src/api/middlewares.ts` is the central Medusa API middleware
  registry
* Request handling mechanics are split between Medusa route/middleware
  declarations and core framework HTTP utilities in
  `packages/core/framework/src/http`

## Request Handling Flow

1. `middlewares.ts` files export middleware config through `defineMiddlewares`.
2. `MiddlewareFileLoader` scans and imports middleware config files.
3. The loader separates route entries into middleware descriptors, body parser
   config routes, additional data validators, and optional error handlers.
4. Body validation writes `req.validatedBody`.
5. Query validation writes `req.validatedQuery`, `req.filterableFields`,
   `req.queryConfig`, `req.remoteQueryConfig`, and list/retrieve config.
6. Route handlers resolve workflows/services and shape HTTP responses.

## Source Evidence

* `packages/medusa/src/api/middlewares.ts` imports domain middleware arrays from
  admin, store, auth, cloud, and hooks surfaces
* `packages/medusa/src/api/admin/products/middlewares.ts` demonstrates matcher,
  method, validator, body parser, upload, and policy declarations
* `packages/medusa/src/utils/define-middlewares.ts` re-exports the framework
  HTTP `defineMiddlewares`
* `packages/core/framework/src/http/middleware-file-loader.ts` loads and
  normalizes middleware files
* `packages/core/framework/src/http/router.ts` applies body-parser routes
* `packages/core/framework/src/http/middlewares/authenticate-middleware.ts`
  supports session, bearer JWT, and API key auth; secret admin API keys set the
  auth context actor type to `api-key`
* `packages/core/framework/src/http/utils/validate-query.ts` and
  `packages/core/framework/src/http/utils/validate-body.ts` populate validated
  request state
* `packages/medusa/src/api/store/carts/[id]/route.ts` and
  `packages/medusa/src/api/admin/products/[id]/route.ts` show route handlers as
  thin orchestration endpoints around workflows, refetch helpers, and response
  shaping

## Graph Evidence

* `search_graph` over Admin `route.ts` handlers found 391 handlers:
  `GET=125`, `POST=196`, `DELETE=70`
* `search_graph` over Store `route.ts` handlers found 51 handlers:
  `GET=27`, `POST=21`, `DELETE=3`
* Source sanity checks matched the graph totals for Admin plus Store handler
  exports

## Query Notes

```text
search_graph(
  project="Users-wuchuni-Desktop-system_design-medusa",
  file_pattern="packages/medusa/src/api/admin/**/route.ts",
  name_pattern="^(GET|POST|PUT|PATCH|DELETE)$"
)

search_graph(
  project="Users-wuchuni-Desktop-system_design-medusa",
  file_pattern="packages/medusa/src/api/store/**/route.ts",
  name_pattern="^(GET|POST|PUT|PATCH|DELETE)$"
)
```

Use `query_graph` with `file_path CONTAINS 'packages/medusa/src/api/admin/'`
for handler counts. During Batch 3, equivalent `STARTS WITH` filters returned
no rows.

## Open Questions

* Generated OpenAPI specs were not cross-checked during this API source ingest
* Auth, cloud, and hooks API surfaces are imported by the central middleware
  registry but were outside the Admin/Store focus of Batch 3

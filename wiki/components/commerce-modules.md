# Commerce Modules

Use this page to route commerce-domain module investigations without building a
large module inventory.

## Verified Facts

* Commerce modules live under `packages/modules/<domain>` and export module
  definitions with `Module(Modules.<DOMAIN>, { service })`
* Main module services usually extend `MedusaService` or
  `ModulesSdkUtils.MedusaService`, pass DML models for generated CRUD, inject
  internal services, and use manager/context/event decorators for custom domain
  methods
* Query graph exposure is configured through `__joinerConfig()` methods and
  `defineJoinerConfig(...)` files where present
* Payment, fulfillment, and tax are provider-backed and use loaders to register
  configured providers and sync provider records
* Region adds a defaults loader

## Ownership Map

* Transactional state: cart and order
* Catalog: product
* Pricing: pricing
* External/provider-mediated operations: payment, fulfillment, tax
* Physical stock/location: inventory and stock-location
* Customer identity and groups: customer
* Discounts and campaigns: promotion
* Region, country, and currency configuration: region

## Routing

Start with module `src/index.ts`, then the main service under `src/services`,
then `src/joiner-config.ts` when present. Use graph searches for
`*ModuleService`, `__joinerConfig`, provider loaders, and event names before
reading broad source trees.

For provider-backed behavior, route from the commerce module loader/service to
the provider implementation under `packages/modules/providers/<provider>`.

## Source Evidence

* Module entrypoints in the Batch 4 scope export domain modules with `Module`
* Main services for cart, order, product, pricing, payment, fulfillment,
  inventory, customer, promotion, region, tax, and stock-location follow the
  generated service plus custom decorated method pattern
* Services often expose single-vs-array overloads and delegate writes to
  underscored transaction methods such as `createRegions_`,
  `createTaxRates_`, or `createStockLocations_`
* DML models keep cross-domain references mostly as scalar IDs rather than
  direct ORM relations, such as order `region_id`, fulfillment item
  `inventory_item_id`, and inventory level `location_id`
* Product, order, fulfillment, inventory, and stock-location provide
  schema-backed joiner configs; payment, pricing, and customer expose lighter
  joiner configs
* Product, inventory, and payment define joiner aliases; product declares
  primary keys including `id` and `handle`
* Order is a high-complexity aggregate boundary with order changes, claims,
  exchanges, returns, credit lines, summaries, transactions, line items,
  shipping, tax lines, and action files
* Inventory adds runtime entity hooks/formulas for stocked, reserved, and
  available quantities
* Event behavior mostly comes from module-service/event decorator
  infrastructure; product also has explicit event builders for category events

## Graph Evidence

* `trace_path(function_name="MedusaService", direction="inbound", mode="calls",
  depth=1)` shows the scoped commerce module services directly using generated
  `MedusaService` behavior

## Open Questions

* Cart, region, promotion, and tax did not expose explicit `src/joiner-config.ts`
  in the same pattern as product, order, inventory, and related modules during
  Batch 4; verify modules-sdk behavior before documenting default joiner
  behavior as a hard rule
* Exact emitted event names for every commerce entity were intentionally not
  enumerated; inspect event constants/builders for event-name changes

## Graph Gaps

* `search_graph(..., label="Class", file_pattern=...)` returned zero for some
  commerce module service classes even though broader searches found them
* Some path-filtered `query_graph` attempts returned no rows while
  `search_graph` returned the expected nodes. Use graph for discovery, then
  verify behavior in source.

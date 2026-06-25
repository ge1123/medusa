# Admin UI and Design System

Use this page to route dashboard, admin extension, admin build tooling, and
design-system investigations.

## Verified Facts

* Dashboard React UI starts in `packages/admin/dashboard`
* Admin extension author helpers live in `packages/admin/admin-sdk`
* Shared extension types, constants, validators, and virtual-module constants
  live in `packages/admin/admin-shared`
* Admin virtual module generation and extension HMR live in
  `packages/admin/admin-vite-plugin`
* Dashboard app and plugin Vite builds live in `packages/admin/admin-bundler`
* Design-system ownership is split across `@medusajs/ui`, `@medusajs/icons`,
  `@medusajs/ui-preset`, and private `@medusajs/toolbox`

## Dashboard Routing

`DashboardApp` accepts dashboard plugins, populates widgets, layouts, menus,
routes, forms, displays, and i18n, splits route extensions into core routes and
`/settings/*` routes, then calls `createRouteMap`.

`createRouteMap` turns route extension paths into nested React Router
`RouteObject[]` while preserving route `loader`, `handle`, child routes, and
parallel routes.

## Dashboard Data

Dashboard data hooks use `@medusajs/js-sdk`, TanStack Query, package-local
`queryKeysFactory`, and mutation invalidation through a shared `queryClient`.
The SDK client is configured from Vite defines such as `__BACKEND_URL__`,
`__AUTH_TYPE__`, and `__JWT_TOKEN_STORAGE_KEY__`, and exposes `window.__sdk` in
browsers.

## Extension Packages

`admin-sdk` owns helpers such as `defineWidgetConfig`, `defineRouteConfig`,
`unstable_defineCustomFieldsConfig`, and `unstable_createFormHelper`. Config
helpers add `$$typeof: Symbol.for("react.memo")` to support Fast Refresh while
exporting config objects.

`admin-shared` owns extension types, constants, validators, custom-field
contracts, and virtual-module constants. Widget injection zones can be extended
by declaration merging `InjectionZoneRegistry`, but runtime validation only
checks the zone shape.

`admin-vite-plugin` generates virtual modules for widgets, routes, menu items,
custom-field links/forms/displays, and i18n. It hashes source subsets and
reloads the corresponding virtual module in dev.

`admin-bundler` owns Vite integration for dashboard app and plugin builds.
Plugin bundling externalizes package dependencies plus React/router/i18n,
js-sdk, admin-sdk, and query packages.

## Design System

`@medusajs/ui` exports components, blocks, hooks, and utilities, including
components such as `Button`, `FocusModal`, `Tooltip`, `Toast`, and
`blocks/data-table`.

`@medusajs/icons` owns icon exports. `@medusajs/ui-preset` injects CSS
variables, typography, light/dark tokens, and Tailwind extensions.
`@medusajs/toolbox` is private tooling for Figma token and icon generation.

## Source Evidence

* `packages/admin/dashboard/src/dashboard-app/dashboard-app.tsx`
* `packages/admin/dashboard/src/dashboard-app/routes/utils.ts`
* `packages/admin/dashboard/src/hooks/api/products.tsx`
* `packages/admin/dashboard/src/lib/client/client.ts`
* `packages/admin/dashboard/src/lib/query-client.ts`
* `packages/admin/dashboard/src/lib/query-key-factory.ts`
* `packages/admin/admin-sdk/src/config/utils.ts`
* `packages/admin/admin-shared/src/extensions/widgets/types.ts`
* `packages/admin/admin-shared/src/extensions/widgets/utils.ts`
* `packages/admin/admin-vite-plugin/src/plugin.ts`
* `packages/admin/admin-vite-plugin/src/routes/generate-routes.ts`
* `packages/admin/admin-vite-plugin/src/routes/generate-menu-items.ts`
* `packages/admin/admin-bundler/src/utils/config.ts`
* `packages/admin/admin-bundler/src/commands/plugin.ts`
* `packages/design-system/ui/src/index.ts`
* `packages/design-system/ui-preset/src/plugin.ts`

## Graph Evidence

* `trace_path(function_name="createRouteMap", direction="inbound")` connects
  `createRouteMap` to `DashboardApp.populateRoutes` and `DashboardApp`
  construction

## Targeted Commands

```text
yarn workspace @medusajs/dashboard typecheck
yarn workspace @medusajs/dashboard test
yarn workspace @medusajs/dashboard lint
yarn workspace @medusajs/ui typecheck
yarn workspace @medusajs/ui test
yarn workspace @medusajs/ui storybook
```

`admin-vite-plugin` has a package-local test script. `admin-sdk`,
`admin-shared`, `admin-bundler`, `ui-preset`, and `toolbox` primarily expose
build scripts. `icons` exposes build, test, and generate scripts.

## Open Questions

* Layout extensions appear planned but disabled in `DashboardApp.populateLayouts`
  with a TODO for a layout virtual module
* Custom injection zones are type-extensible but only regex-validated at
  runtime; do not assume runtime existence from types alone

## Graph Gaps

* `search_graph(file_pattern=...)` found dashboard and design-system nodes, but
  equivalent `query_graph` filters on `n.file_path STARTS WITH ...` returned no
  rows during Batch 6
* `trace_path(medusaVitePlugin, outbound)` returned unrelated short-name
  results; source inspection is stronger for admin-vite-plugin behavior

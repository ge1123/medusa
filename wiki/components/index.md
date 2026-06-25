# Component Routing

Use this page to choose where to begin. Then use graph tools to find the exact
symbols, routes, and relationships.

## Verified Facts

* This is a Yarn 3.2.1 TypeScript monorepo with Turbo pipelines at the root
* Root workspaces include `packages/medusa`, `packages/core/*`,
  `packages/modules/*`, `packages/modules/providers/*`, `packages/admin/*`,
  `packages/design-system/*`, `packages/cli/*`, `packages/plugins/*`,
  `packages/generated/*`, and `integration-tests/**/*`
* Backend packages primarily use Jest; admin/dashboard and design-system UI use
  Vitest
* API routes live in the main `@medusajs/medusa` package under
  `packages/medusa/src/api`

## Routing Table

* Framework runtime, HTTP, app config, and DI:
  `packages/core/framework`
  * Deep note: `core-runtime.md`
* Shared types and DTOs:
  `packages/core/types`
* Utilities, errors, DAL, DML, module-service helpers, decorators:
  `packages/core/utils`
* Workflow SDK primitives:
  `packages/core/workflows-sdk`
* Built-in business workflows and steps:
  `packages/core/core-flows`
* Orchestration internals:
  `packages/core/orchestration`
* Module SDK:
  `packages/core/modules-sdk`
* JS client SDK:
  `packages/core/js-sdk`
  * Deep note: `cli-sdk-tooling.md`
* Medusa package, API routes, app/middleware behavior:
  `packages/medusa`
* Admin API routes:
  `packages/medusa/src/api/admin`
* Store API routes:
  `packages/medusa/src/api/store`
  * Deep note: `medusa-api.md`
* Commerce modules:
  `packages/modules/<domain>`
  * Deep note: `commerce-modules.md`
* Provider implementations:
  `packages/modules/providers/<provider>`
* Event bus, cache, locking, workflow engines:
  `packages/modules/event-bus-*`, `packages/modules/cache-*`,
  `packages/modules/locking`, `packages/modules/workflow-engine-*`
  * Deep note: `infrastructure-modules.md`
* Optional plugins:
  `packages/plugins`
* Admin SDK and shared extension code:
  `packages/admin/admin-sdk`, `packages/admin/admin-shared`
* Admin build tooling:
  `packages/admin/admin-bundler`, `packages/admin/admin-vite-plugin`
* Dashboard React application:
  `packages/admin/dashboard`
* Design system:
  `packages/design-system/ui`, `packages/design-system/icons`,
  `packages/design-system/ui-preset`, `packages/design-system/toolbox`
  * Deep note: `admin-ui.md`
* CLI and project creation:
  `packages/cli/medusa-cli`, `packages/cli/create-medusa-app`,
  `packages/cli/medusa-dev-cli`
* OpenAPI and HTTP type generation:
  `packages/cli/oas`, `packages/cli/http-types-generator`
* Telemetry:
  `packages/medusa-telemetry`
  * Deep note: `cli-sdk-tooling.md`
* Test helpers:
  `packages/medusa-test-utils`, `integration-tests/helpers`
* Integration test suites:
  `integration-tests/api`, `integration-tests/http`,
  `integration-tests/modules`
* Docs site and docs automation:
  `www`
* Book docs:
  `www/apps/book/app`, `www/apps/book/sidebar.mjs`
* Resources docs:
  `www/apps/resources/app`, `www/apps/resources/sidebars`
* UI docs:
  `www/apps/ui/app`, `www/apps/ui/specs/examples`, `www/apps/ui/sidebar.mjs`
* User Guide:
  `www/apps/user-guide/app`, `www/apps/user-guide/sidebar.mjs`
* Cloud docs:
  `www/apps/cloud/app`, `www/apps/cloud/sidebar.mjs`
* Docs UI and shared docs packages:
  `www/packages`
* Generated or externally managed docs surfaces:
  `www/apps/resources/references`, `www/apps/ui/specs/components`,
  `www/apps/api-reference`
  * Deep note: `docs-generated-surfaces.md`
* Release metadata:
  `.changeset`
  * Deep note: `docs-generated-surfaces.md`
* CI and GitHub automation:
  `.github/workflows`, `.github/actions`
* Maintenance scripts:
  `scripts`
  * Deep note: `cli-sdk-tooling.md`

## Source Evidence

* `package.json`: workspace layout, root scripts, Yarn version
* `.yarnrc.yml`: `nodeLinker: node-modules`, Yarn release path
* `turbo.json`: root build and test pipeline names
* `jest.config.js`: root Jest project discovery for package workspaces
* `packages/admin/dashboard/package.json`: dashboard `typecheck`, `test`, and
  `lint` scripts
* `packages/design-system/ui/package.json`: UI `typecheck`, Vitest, and
  Storybook scripts
* Removed `.claude/skills/writing-docs` and related reference files: docs
  project routing, generated-directory cautions, MDX metadata conventions, and
  prose style were migrated into `AGENTS.md`
* Removed `.claude/skills/writing-tsdocs`: public TSDoc scope and supported
  Medusa custom tags were migrated into `AGENTS.md`

## Graph Evidence

* `get_architecture(aspects=["all"])` on
  `Users-wuchuni-Desktop-system_design-medusa` reported TypeScript as the
  dominant language, route nodes, package clusters for `admin`, `core`,
  `modules`, `medusa`, `cli`, `design-system`, and `www`, and hotspots around
  workflow, context, DML, and dashboard helpers

## Open Questions

* Some generated packages under `packages/generated/*` were not inspected for
  current contents during initial wiki creation
* Full graph indexing was not run; the initial repository-specific index used
  fast mode
* The initial wiki does not preserve every detailed docs style rule from the old
  Claude skills; inspect existing docs pages before substantial MDX edits

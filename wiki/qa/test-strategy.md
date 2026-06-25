# Test and QA Strategy

Use this page to pick targeted tests for code changes without running broad
suites by default.

## Verified Facts

* Root integration commands route through Turbo:
  * `yarn test:integration:api` filters to `integration-tests-api`
  * `yarn test:integration:http` filters to `integration-tests-http`
  * `yarn test:integration:modules` filters to `integration-tests-modules`
  * `yarn test:integration:packages` runs package-level `test:integration`
* Package integration tests are split into fast and slow groups
* Slow package integration suites are `workflow-engine-redis`, `index`,
  `product`, `order`, and `cart`
* CI shards package integration tests by fast/slow group over 3 shards
* CI shards HTTP and modules integration tests over 4 shards
* HTTP and modules integration workspaces use Jest with SWC and node
  environment

## Routing

For API route behavior, start with
`integration-tests/http/__tests__/<domain>`.

For cross-module workflow/API behavior, check
`integration-tests/modules/__tests__/<domain>` when the route delegates into
workflows or module orchestration.

For module-service, provider, DAL, workflow-engine, or core utility internals,
use package-local `integration-tests/__tests__` and the package workspace
`test:integration` script.

For shared full-stack test harness behavior, start in
`packages/medusa-test-utils`. For full-stack suite convenience helpers, start
in `integration-tests/helpers`.

## Targeted Commands

```text
yarn test:integration:http -- --testPathPattern=<path-or-pattern>
yarn test:integration:modules -- --testPathPattern=<path-or-pattern>
yarn test:integration:api
yarn workspace <workspace-name> test:integration -- --testPathPattern=<path-or-pattern>
yarn workspace @medusajs/test-utils test
yarn test:integration:packages:fast
yarn test:integration:packages:slow
```

## Helper Ownership

`packages/medusa-test-utils` owns reusable test runners and DB lifecycle:
`medusaIntegrationTestRunner`, `moduleIntegrationTestRunner`, DB create,
snapshot, restore, teardown, shutdown, and mock event bus support.

`integration-tests/helpers` owns suite-level convenience helpers such as admin
user creation, publishable/store headers, storefront defaults, fixtures, retry,
and deterministic index waiting.

Prefer `waitForIndexedEntities` for index synchronization assertions instead of
arbitrary timeouts.

## Source Evidence

* Root `package.json`
* `turbo.json`
* `.github/workflows/action.yml`
* `define_jest_config.js`
* `integration-tests/package.json`
* `integration-tests/http/package.json`
* `integration-tests/http/jest.config.js`
* `integration-tests/modules/package.json`
* `integration-tests/modules/jest.config.js`
* `packages/medusa-test-utils/src/medusa-test-runner.ts`
* `packages/medusa-test-utils/src/module-test-runner.ts`
* `packages/medusa-test-utils/src/database.ts`
* `integration-tests/helpers/create-admin-user.ts`
* `integration-tests/helpers/seed-storefront-defaults.ts`
* `integration-tests/helpers/wait-for-index.ts`

## Graph Evidence

* `search_graph(qn_pattern=".*integration-tests\\.http.*testSuite")` found 102
  HTTP `testSuite` symbols
* `search_graph(qn_pattern=".*integration-tests\\.modules.*testSuite")` found
  107 module `testSuite` symbols
* `search_graph(qn_pattern=".*packages\\.medusa-test-utils\\.src.*")` found
  test runner, DB, event, and helper symbols

## Open Questions

* `integration-tests/api` has root/package scripts, but current source stores
  test bodies as `.js.txt` and `.ts.txt`; verify its generation/runnable path
  before relying on it for active coverage
* The modules CI job exports `CHUNK` and `CHUNKS`, but the inspected matrix block
  only defined `shard_index`; verify whether this is harmless leftover env or
  workflow drift before editing CI

## Graph Gaps

* `search_graph(file_pattern="integration-tests/(api|http|modules|helpers).*")`
  returned no rows, while qn-pattern and BM25 queries found many test symbols
* Graph marks many test symbols with `is_test=false`; do not rely on that
  property for integration test detection

# CLI, SDK, Telemetry, and Tooling

Use this page to route CLI, generated HTTP type, OpenAPI, JS SDK, telemetry,
and repository tooling investigations.

## Verified Facts

* CLI routing starts in `packages/cli/medusa-cli`; the published bin is
  `medusa`
* Most `medusa` project commands are registered in `src/create-cli.ts` but
  delegate to `@medusajs/medusa/commands/*` resolved from the consumer project
* Project creation is split between `medusa new` in `@medusajs/cli` and the
  standalone `create-medusa-app` package
* Contributor-only package development helpers live in
  `packages/cli/medusa-dev-cli`
* HTTP type generation lives in `packages/cli/http-types-generator`; root
  scripts are `yarn generate:http-types` and `yarn validate:http-types`
* OpenAPI generation lives under `packages/cli/oas`; root
  `yarn openapi:generate` runs the GitHub CI wrapper
* JS SDK client work starts in `packages/core/js-sdk`; it is an HTTP client
  layer, not the HTTP type generator
* Anonymous usage telemetry starts in `packages/medusa-telemetry`; framework
  OpenTelemetry tracing is separate
* `scripts/` is mostly CI glue and repository maintenance tooling, not runtime
  behavior

## CLI Command Architecture

`@medusajs/cli` uses yargs. `buildLocalCommands` registers commands such as
`new`, `db:*`, `plugin:*`, `telemetry`, `codemod`, `develop`, `start`, `build`,
`lint`, `user`, `exec`, and `mcloud`.

Project-runtime commands resolve `@medusajs/medusa/commands/<command>` from the
consumer project with `resolve-cwd`, inject runtime metadata such as
`directory`, `sitePackageJson`, and `useYarn`, then delegate. Commands that
start long-running processes intentionally keep the handler promise pending so
the CLI wrapper does not exit early.

## Generated HTTP Types

`medusa-http-types generate` scans configured `validators.ts` globs, extracts
exported Zod schemas with TypeScript, resolves types, maps route directories to
HTTP domains, classifies generated interfaces into `payloads.ts` or
`queries.ts`, and updates HTTP type index files.

`medusa-http-types validate` compares validator-derived types against generated
HTTP type files. CI mode exits non-zero on incompatibility.

The monorepo config is `http-types.config.json`, with validators from
`packages/medusa/src/api/{admin,store}/*/validators.ts` and output to
`packages/core/types/src/http`.

## OpenAPI Workflow

`medusa-oas oas` builds Admin, Store, or combined OAS from public docs downloads
or local monorepo generated OAS source when `--local` is set.

`medusa-oas docs` sanitizes, splits, previews, and builds Redocly-ready API
reference files.

`packages/cli/oas/oas-github-ci/scripts/build-openapi.js` runs Admin and Store
generation and writes split plus full files for docs/reference workflows.

## JS SDK

`packages/core/js-sdk` exports `Medusa`, `Client`, `Admin`, `Store`, and `Auth`.
The client handles base URL normalization, API key/basic auth, publishable key
headers, JWT storage, session credentials, locale headers/localStorage, JSON
request/response handling, query string serialization, `FetchError`, and SSE
through `fetchStream`.

## Telemetry

`@medusajs/telemetry` queues anonymous usage events in outbox files, flushes on
thresholds/interval/process exit, and dispatches in a detached child process.

`MEDUSA_DISABLE_TELEMETRY` stops event writes. `MEDUSA_TELEMETRY_HOST` and
`MEDUSA_TELEMETRY_PATH` redirect the dispatch target. Verbose diagnostics use
`MEDUSA_TELEMETRY_VERBOSE`.

## Maintenance Scripts

Use `scripts/` for repository automation such as changed-file guards,
generated-file commit assertions, issue/PR helper scripts, label/comment
automation, release notification formatting, dependency analysis, and chunked
workspace unit tests.

## Source Evidence

* `packages/cli/medusa-cli/src/create-cli.ts`
* `packages/cli/create-medusa-app/src/commands/create.ts`
* `packages/cli/medusa-dev-cli/src/index.js`
* `packages/cli/http-types-generator/src/commands/generate.ts`
* `packages/cli/http-types-generator/src/commands/validate.ts`
* `http-types.config.json`
* `packages/cli/oas/medusa-oas-cli/src/command-oas.ts`
* `packages/cli/oas/medusa-oas-cli/src/command-docs.ts`
* `packages/cli/oas/oas-github-ci/scripts/build-openapi.js`
* `packages/core/js-sdk/src/client.ts`
* `packages/medusa-telemetry/src/telemeter.js`
* `scripts/assert-codegen-build-committed-actions.sh`
* `scripts/assert-changed-files-actions.sh`
* `scripts/run-workspace-unit-tests-in-chunks.sh`

## Open Questions

* Root `package.json` has `release:next` using `chgstangeset publish ...`;
  verify whether this is reachable or stale before changing release automation
* `create-medusa-app` and `medusa new` both exist; verify current product
  guidance before documenting one as preferred over the other
* `medusa-dev-cli` is documented here as contributor tooling, but it still
  publishes a package/bin

## Graph Gaps

* During Batch 7, `search_graph` found CLI, OAS, HTTP type, telemetry, and JS
  SDK symbols, but `query_graph` path filters returned empty rows for the same
  areas. Treat path-filter misses as graph/tooling staleness, not absence proof.

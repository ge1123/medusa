# Reusable Q&A

Use this index for stable investigated answers that are likely to be asked
again. Keep answers short and link to dedicated QA pages when they grow.

## Current Answers

* Test and QA strategy: `test-strategy.md`

## Seed Answers

### Which tool should agents use first for code discovery?

Use `codebase-memory-mcp` first for symbols, routes, functions, classes,
relationships, and architecture. Use `rg` or file search for literals, config,
scripts, Markdown, generated files, or graph gaps.

### Where do API route changes usually start?

Start in `packages/medusa/src/api/admin` or `packages/medusa/src/api/store`,
then follow workflow calls into `packages/core/core-flows` and domain services
under `packages/modules/<domain>`.

### Where do workflow changes usually start?

Start in `packages/core/core-flows/src/<domain>` for built-in workflows and
steps. Use `packages/core/workflows-sdk` only when changing the workflow
composition engine itself.

### Where do documentation changes usually start?

Start in `www/apps/<project>/app` for MDX content and the matching sidebar file
for navigation. For generated references, fix upstream TSDoc or HTTP types under
`packages/` unless the task explicitly targets generated output.

### What should API PR reviews check first?

Check the route under `packages/medusa/src/api`, corresponding HTTP integration
tests under `integration-tests/http/__tests__`, Zod validation, HTTP type
updates, typed request/response generics, authentication middleware, pagination
for list routes, and changesets for package source changes.

## Evidence

* Root `package.json`, `CLAUDE.md` migration notes, `.claude/skills` migration
  notes, and graph architecture output from the initial wiki creation

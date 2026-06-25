# Docs and Generated Surfaces

Use this page to route docs work and avoid hand-editing generated or externally
managed surfaces.

## Verified Facts

* Docs live under `www`, a Yarn workspace root with `apps/*` and `packages/*`
* Main docs apps:
  * `www/apps/book`: learning docs
  * `www/apps/resources`: resources, recipes, integrations, module docs, and
    references
  * `www/apps/ui`: Medusa UI docs
  * `www/apps/user-guide`: dashboard user guide
  * `www/apps/cloud`: Cloud docs
  * `www/apps/api-reference`: Admin and Store API reference
* Shared docs code lives under `www/packages`, including build scripts, docs UI,
  docs utilities, remark/rehype plugins, tags, Tailwind, tsconfig, and shared
  types
* MDX content pages generally define `export const metadata = { title: ... }`
* Book MDX pages usually keep `${pageNumber}` in `metadata.title`
* Sidebars are explicit `sidebar.mjs` files; resources also splits sidebars
  under `www/apps/resources/sidebars`
* Per-app `prep` scripts generate sidebars, edit dates, files maps, slug maps,
  specs indexes, and API-reference manifests

## Generated Boundaries

Treat these as generated or externally managed unless the task explicitly
targets generated artifacts:

* `www/apps/resources/references`
* `www/apps/ui/specs/components`
* `www/apps/api-reference/specs`
* `www/apps/api-reference/generated`
* app-level `www/apps/*/generated`

The API reference README says OpenAPI spec changes should be made in
`packages/medusa/src/api` instead of directly editing generated spec files.

## Source Evidence

* `www/package.json`
* `www/apps/*/package.json`
* `www/apps/{book,resources,ui,user-guide,cloud}/sidebar.mjs`
* `www/apps/resources/sidebars/*.mjs`
* `www/apps/*/scripts/prepare.mjs`
* `www/packages/build-scripts/src/generate-sidebar.ts`
* `www/packages/build-scripts/src/generate-split-sidebars.ts`
* `www/packages/build-scripts/src/generate-edited-dates.ts`
* `www/apps/api-reference/README.md`
* `www/apps/ui/scripts/generate-specs-index.mjs`
* `www/apps/ui/scripts/upload-specs-to-r2.mjs`

## Graph Evidence

* `get_architecture(aspects=["packages","clusters"])` identifies a `www`
  cluster, but graph evidence is only orientation for this area because most
  docs ownership lives in MDX, sidebars, generated files, and package scripts

## Release Metadata

Changesets live in `.changeset`. `.changeset/config.json` uses GitHub changelog
generation, `baseBranch: develop`, public access, fixed package groups, ignored
integration-test packages, and patch updates for internal dependencies.

Root package scripts and `.github/workflows/release.yml` drive package
versioning and publishing with Changesets. `.github/release.yml` groups
generated GitHub changelog entries into Features, Bugs, Documentation, Chores,
and Other Changes.

Human-facing release notes should follow `AGENTS.md`: optional Highlights,
then Features, Bugs, Documentation, Chores, Other Changes, New Contributors,
and a final Full Changelog link.

## Open Questions

* `.github/workflows/draft-release.yml` references a `/writing-releases` skill,
  but no `.claude` skill files are present in this checkout; `AGENTS.md`
  contains the release-note convention currently available to agents
* The exact regeneration command that owns `www/apps/resources/references`
  should be verified before regenerating that tree
* The exact upstream docgen source for `www/apps/ui/specs/components` JSON
  should be verified before regenerating or editing those files

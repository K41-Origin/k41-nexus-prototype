# Infrastructure Audit Record

**Status:** PARTIAL — integrated static infrastructure review published. Build graph and test/CI configuration are statically mapped; direct and lockfile-transitive dependency resolution mapped (GOV-DEPMAP-002); source import inventory across 334 code modules mapped (GOV-DEPMAP-003); runtime/package/bundle verification remain OPEN.

## Ownership

- [Infrastructure index](../../infrastructure/README.md)
- [Build system](../../infrastructure/BUILD-SYSTEM.md)
- [Dependencies](../../infrastructure/DEPENDENCIES.md)
- [Testing](../../infrastructure/TESTING.md)
- [CI/CD](../../infrastructure/CI-CD.md)
- [Scripts audit](../../SCRIPTS-AUDIT.md) — existing 14/14 static review; BUILD-001–BUILD-007.
- [GitHub CI audit](../../github-ci/DEEP-AUDIT.md) — existing 10/10 workflow/config review; BUILD-008–BUILD-014.

## Reconciliation

- Root package and lockfile: npm workspaces `server` and `webview-ui`; lockfile v3, 816 package records.
- Build: AsyncAPI generation/validation → TypeScript/lint → esbuild extension/CLI/hooks/PTy/uninstall outputs → Vite webview/asset sidecars → npm/VSIX package lifecycle.
- Dependencies: root production Fastify dependencies are external CLI runtime requirements; native PTY is optional/external and resolved through variable require; server and webview workspaces carry their own dependency groups; pngjs is reached by core asset decoding and the Node Vite asset pipeline, while actual bundle/package placement remains unverified.
- Source import map: 334 TS/JS modules across core/server/webview/adapters/scripts have been statically inventoried. Cross-layer edges and terminal/process ownership are documented in SYSTEM-DEPENDENCY-MAP.md (GOV-DEPMAP-003/004).
- Tests: Vitest webview/server, Node package-contract, Playwright E2E; no dedicated core test project. CI maps protocol drift, static checks, unit tests, package smoke, npm audit and a 3-OS × 3-shard E2E matrix; however, AsyncAPI validation, generated-message drift and E2E inventory drift are advisory due to `continue-on-error` and omission from the final gate (BUILD-014).
- CI/CD: existing CI/release static review is reused; BUILD-008/009 remediation remains on the separate security branch pending merge and verification. BUILD-010–BUILD-014 remain OPEN. Settings and actual workflow outcomes remain OPEN.

## Findings and boundaries

Preserve BUILD-001–BUILD-014 in their owning scripts/CI records; BUILD-001–BUILD-007 belong to scripts, BUILD-008–BUILD-014 to GitHub CI/release. No new finding ID was created from the static integration map. No test, build, lint, typecheck, install, package, audit, CI run or runtime experiment was executed. No application source, manifest, dependency or workflow was modified in this infrastructure audit.

## Next action

When authorized, run the controlled verification plan in this order: YAML/actionlint and workflow parser; npm ci/build/typecheck/lint/unit tests; package smoke on Node 20/22 and OS targets; E2E matrix; inspect bundle/tarball/VSIX; reconcile dependency/SBOM/license evidence; then verify GitHub settings and actual workflow outcomes. Do not close existing findings based on static configuration alone.

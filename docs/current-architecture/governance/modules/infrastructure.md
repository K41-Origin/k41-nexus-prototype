# Infrastructure Audit Record

**Status:** PARTIAL — integrated static infrastructure review published at source snapshot `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`. Build graph and test/CI configuration are statically mapped; direct dependencies classified; runtime/package/bundle verification remains OPEN.

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
- Dependencies: root production Fastify dependencies are external CLI runtime requirements; native PTY is optional/external; server and webview workspaces carry their own dependency groups; pngjs is a build/bundle reachability question rather than a proven packaging defect.
- Tests: Vitest webview/server, Node package-contract, Playwright E2E; no dedicated core test project. CI maps protocol drift, static checks, unit tests, package smoke, npm audit and a 3-OS × 3-shard E2E matrix; however, AsyncAPI validation, generated-message drift and E2E inventory drift are advisory due to `continue-on-error` and omission from the final gate (BUILD-014).
- CI/CD: existing CI/release static review is reused; BUILD-008/009 remediation remains on the separate security branch pending merge and verification. BUILD-010–BUILD-014 remain OPEN. Settings and actual workflow outcomes remain OPEN.

## Findings and boundaries

Preserve BUILD-001–BUILD-014 in their owning scripts/CI records; BUILD-001–BUILD-007 belong to scripts, BUILD-008–BUILD-014 to GitHub CI/release. No new finding ID was created from the static integration map. No test, build, lint, typecheck, install, package, audit, CI run or runtime experiment was executed. No application source, manifest, dependency or workflow was modified in this infrastructure audit.

## Next action

When authorized, run the controlled verification plan in this order: YAML/actionlint and workflow parser; npm ci/build/typecheck/lint/unit tests; package smoke on Node 20/22 and OS targets; E2E matrix; inspect bundle/tarball/VSIX; reconcile dependency/SBOM/license evidence; then verify GitHub settings and actual workflow outcomes. Do not close existing findings based on static configuration alone.

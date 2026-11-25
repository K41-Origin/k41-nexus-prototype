# Infrastructure — Current Architecture

**Status:** PARTIAL — integrated static infrastructure map published. Build/package flow and test/CI topology are statically mapped; direct dependency classes are mapped; exhaustive transitive dependency/bundle verification and runtime execution remain OPEN.

## Document ownership

- [Build system](BUILD-SYSTEM.md) — package/workspace scripts, TypeScript projects, bundlers, generated protocol and distribution boundary. STATIC CONFIGURATION AUDIT COMPLETE.
- [Dependencies](DEPENDENCIES.md) — root/workspace direct dependencies, major consumers, optional/native packages and supply-chain constraints. PARTIAL; transitive graph and bundle proof OPEN.
- [Testing](TESTING.md) — test frameworks/configurations, test inventory, CI suite mapping and execution boundaries. STATIC INVENTORY COMPLETE; test execution OPEN.
- [CI/CD](CI-CD.md) — workflow configuration and integration with build/release tooling. PARTIAL; settings/runtime verification OPEN.
- [Scripts static audit](../SCRIPTS-AUDIT.md) — 14/14 files under `scripts/`, BUILD-001–BUILD-007.
- [GitHub CI/release audit](../github-ci/DEEP-AUDIT.md) — 10/10 historical `.github/` files, BUILD-008–BUILD-013; BUILD-008/009 remediation on a separate unmerged branch.

## Integrated findings and constraints

- The root npm workspace orchestrates `server` and `webview-ui`; `core/` is source-shared rather than a separate npm workspace.
- AsyncAPI YAML generates the shared ClientMessage/ServerMessage TypeScript contract.
- esbuild creates extension/CLI/hook/uninstall outputs; Vite builds the webview and asset metadata sidecars.
- Runtime Fastify dependencies remain external for the standalone CLI; native PTY is optional and external.
- Unit tests, package smoke and E2E are separate suites with different runners/configurations.
- CI has a Node 20 package smoke job and a 3-OS × 3-shard E2E matrix; the aggregate required-check job is configured in YAML, but repository/organization enforcement remains an external settings question.
- BUILD-001–BUILD-013 and module findings remain open according to their owning records. Configuration and test inventory are not runtime evidence.

See [repository audit register](../governance/AUDIT-REGISTER.md), [infrastructure governance record](../governance/modules/infrastructure.md) and [evidence log](../governance/EVIDENCE-LOG.md).

## Remaining work

1. Inspect generated build outputs, bundle externals and npm/VSIX contents when execution is authorized.
2. Complete transitive dependency, license, SBOM and optional native dependency analysis.
3. Execute the prescribed test/build/typecheck/lint/package matrix only with authorization and record exact commands/results.
4. Verify GitHub Environment, rulesets, token/secret scopes, npm trusted publisher and actual release provenance.
5. Reconcile the CI workflow's aggregate check with actual branch protection/organization policy; preserve BUILD-010.

No source, package manifest or dependency changed in this infrastructure audit. No tests, build, lint, typecheck, package, dependency audit or workflow run was executed.

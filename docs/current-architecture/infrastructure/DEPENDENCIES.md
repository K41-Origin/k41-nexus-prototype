# Dependency Inventory

**Status:** PARTIAL — direct declarations and npm lockfile transitive closure statically mapped (GOV-DEPMAP-002); exhaustive source import/use mapping and bundle verification remain OPEN.

**Lockfile source snapshot:** `main@b1104a808cf70382c2bd14a94d372d907a500cda`; package-lock blob `e183f6eced2e598496010a79c56a19f32e3dbe83`.
**Security workflow snapshot:** `security/remediate-build-008-009@1b71acd07b48d56527d0ad72e4d5e23713b390a7`.

## Lock and workspace identity

- Root package: `pixel-agents@1.6.0`.
- npm workspaces: `server`, `webview-ui`.
- Lockfile: `package-lock.json`, lockfileVersion 3, 816 package records.
- Node engine: `>=20`; `.nvmrc`: 22.
- `server` is a private workspace with a separate manifest; `webview-ui` is a private workspace with its own manifest.
- The lockfile root snapshot matches the root manifest's declared workspace set and direct dependency groups. This is a static manifest/lock comparison, not an `npm ci` result.

## Root production and optional dependencies

| Package | Declaration | Consumer / purpose | Packaging implication |
|---|---|---|---|
| `fastify` | Root dependency | Fastify HTTP server used by `server/src/httpServer.ts`; external in CLI bundle | Must remain installable at package runtime |
| `@fastify/cors` | Root dependency | CORS registration in HTTP server | External CLI runtime dependency |
| `@fastify/static` | Root dependency | Standalone SPA serving | External CLI runtime dependency |
| `@fastify/websocket` | Root dependency | Control-plane WebSocket route | External CLI runtime dependency |
| `@lydell/node-pty` | Root optional dependency | Native PTY runtime, dynamically loaded and external to esbuild | Absence degrades terminal capability; platform/native install must be verified |

The root CLI bundle external list in `esbuild.js` explicitly matches the Fastify packages and PTY modules above. `scripts/npm-package-contract.mjs` records why Fastify packages are root runtime dependencies even though server source is bundled: the CLI requires them at runtime. The optional PTY module is not a mandatory package install.

## Server workspace dependencies

| Package | Declared group | Consumer / role |
|---|---|---|
| `fastify`, `@fastify/cors`, `@fastify/static`, `@fastify/websocket` | dependencies | HTTP/WebSocket server; duplicated workspace declaration alongside root package runtime deps |
| `@xterm/headless`, `@xterm/addon-serialize` | dependencies | Terminal screen model and replay/serialization |
| `ws` | dependencies | WebSocket runtime/type ecosystem; exact direct runtime call sites were not exhaustively mapped in this pass |
| `vitest`, `allure-vitest`, `@types/ws` | devDependencies | Server unit tests and reporting/type support |

## Webview workspace dependencies

| Package | Declared group | Consumer / role |
|---|---|---|
| `react`, `react-dom` | dependencies | UI component/runtime |
| `@xterm/xterm`, `@xterm/addon-fit` | dependencies | Browser terminal rendering and sizing |
| `vite`, `@vitejs/plugin-react`, `tailwindcss`, `@tailwindcss/vite` | devDependencies | Browser dev/build pipeline |
| `typescript`, `typescript-eslint`, `eslint`, React ESLint plugins | devDependencies | Type checking and static analysis |
| `vitest`, `allure-vitest` | devDependencies | Webview unit tests and optional reporting |
| `pngjs`, `@types/pngjs` | devDependencies | Vite build-time asset decoding and test support; not a declared browser runtime dependency |
| `tsx` | devDependencies | TypeScript tooling |

## Root development/tooling dependencies

The root devDependency set includes AsyncAPI Modelina/parser, Playwright, VS Code test-electron, Allure, esbuild, ESLint and plugins, TypeScript, tsx, npm-run-all, Prettier, Knip, Husky/lint-staged, pngjs and semver. These support protocol generation/validation, tests, build, static checks and packaging verification. This table classifies roles from manifest/config/source evidence; it is not a complete transitive dependency graph.

## Internal dependency boundaries

- `core/` is shared host-neutral contracts/domain utilities plus a Node-only asset I/O/PNG subsystem. Static core review found no upward imports from server, webview or adapters.
- `server/` consumes core contracts/utilities and Node facilities; it is consumed by both the VS Code adapter and standalone CLI.
- `adapters/vscode/` imports concrete server services and VS Code APIs; it is a host composition layer, not a generic plugin-independent adapter boundary.
- `webview-ui/` imports core contracts and browser-safe helpers; it does not import server or adapter implementation modules. It chooses PostMessage in VS Code and WebSocket in standalone browser.
- Build tooling imports core asset functions from Vite configuration; this Node-only path is build-time.
- `core/src/messages.ts` is generated from `core/asyncapi.yaml`; the YAML is the protocol source of truth.

## Supply-chain and migration constraints

- Lockfile v3 is committed; CI uses `npm ci` and npm audit across workspaces plus root.
- Overrides pin vulnerable/transitive dependency families including `jsonpath-plus`, `brace-expansion`, `minimatch`, and scoped AJV overrides. The reason and current resolved versions should be revisited against future npm audit evidence, not inferred from override presence.
- Native PTY package requires OS/ABI verification; optional dependency absence is an intentional degraded mode.
- Fastify remains external in the CLI bundle; moving it to devDependencies would break the packaged CLI.
- `pngjs` packaging depends on bundling reachability; the current static configuration suggests it is bundled, but no built artifact was inspected.
- No complete license, provenance, SBOM, vulnerability-resolution or transitive import graph was produced in this audit.

## Next verification

1. Run `npm ci` on supported Node/OS targets and record optional PTY install outcomes.
2. Inspect esbuild/Vite output or metafiles to prove which dependencies are bundled vs external.
3. Verify npm tarball dependency closure and installed CLI smoke on Node 20/22 and supported platforms.
4. Generate a dependency graph/SBOM and reconcile direct/transitive license and vulnerability policy.
5. Trace `ws` and other suspected unused/duplicated packages before considering removal.

No package install, build, dependency audit, tests or bundle inspection was executed.


## Transitive npm lockfile graph — GOV-DEPMAP-002 (2026-11-26)

**Source:** `package-lock.json` blob `e183f6eced2e598496010a79c56a19f32e3dbe83` (lockfileVersion 3; 816 package records, 812 `node_modules/` entries). Manifest identities: root `package.json` `66efe28fd2927466c5f8283c6e26fe63da427079`; `server/package.json` `f3e7e63e1920d6804eec70c5104914c436269351`; `webview-ui/package.json` `3729872b2736eaf6b66c608c1b40f4e24c25d2fb`.

The lock graph was traversed from each package/workspace root, resolving nested `node_modules` locations and following runtime, optional, workspace-root dev and peer-dependency edges. Optional peer dependencies are not treated as missing required edges.

| Root | Direct manifest dependencies (all groups) | Reachable locked package paths | Resolved edges (runtime / dev / optional / peer) | Unresolved required edges |
|---|---:|---:|---:|---:|
| Root | 28 | 664 | 1,055 / 23 / 76 / 44 (1,198 total) | 0 |
| Server workspace | 10 | 200 | 174 / 4 / 55 / 36 (269 total) | 0 |
| Webview workspace | 25 | 293 | 295 / 21 / 78 / 66 (460 total) | 0 |

The union contains 810 reachable installed package paths. Two additional `node_modules/` entries (`pixel-agents` and `webview-ui`) are workspace-link records, not unresolved external packages. The lockfile records 643 distinct package names; nested duplicate instances are retained where npm resolution requires them.

**Interpretation:** this closes the static transitive dependency *lockfile-resolution* inventory for the current committed lockfile. It does not prove that `npm ci` succeeds, that every package is imported/reachable in shipped bundles, that optional native binaries install on every OS/ABI, or that licenses/vulnerabilities are acceptable. Source import-to-package usage, esbuild/Vite metafile comparison, tarball/VSIX closure, SBOM and installed-runtime verification remain OPEN.

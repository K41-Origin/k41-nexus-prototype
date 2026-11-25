# Build System

**Status:** STATIC CONFIGURATION AUDIT COMPLETE — integrated build/package flow mapped at `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`. Build execution and produced-artifact verification remain OPEN.

## Scope and evidence

Source snapshot: `K41-Origin/k41-nexus-prototype`, branch `security/remediate-build-008-009`, commit `86d3b1dbddae7605e5e2092886c12982f8e4bf58`. The complete recursive tree contains 856 entries, `truncated=false`. The branch differs from `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` in exactly the two workflow blobs documented in the GitHub CI audit; build source/config below is unchanged.

| File | Blob SHA | Static scope |
|---|---|---|
| `package.json` | `66efe28fd2927466c5f8283c6e26fe63da427079` | Workspaces, scripts, package files, engines and dependency classes |
| `package-lock.json` | `e183f6eced2e598496010a79c56a19f32e3dbe83` | Lockfile v3, 816 package records, root/workspace snapshots |
| `esbuild.js` | `450d0d3a1e2e194d47d5bb598c1669b9f3c33955` | Extension/CLI/hooks/PTy/uninstall bundle outputs and assets |
| `tsconfig.json` | `3f987e647c0a6e171f29268898fccc9dc8516a25` | Root TypeScript project |
| `webview-ui/vite.config.ts` | `b47531773bf6fea57fe6d46a6291133371aa56ea` | Webview bundling and build-time asset plugin |
| `webview-ui/vitest.config.ts` | `3ed45c68d601468600716a0fd69b8cdb12739f16` | Webview unit-test configuration |
| `server/vitest.config.ts` | `b8d6265259e3abcec1d93945bbf42c43618b62d0` | Server unit-test configuration |
| `e2e/playwright.config.ts` | `4e4683186ee24e5cdce49a35cf336e069f35b1a2` | E2E suite configuration |
| `scripts/generate-messages.ts` | `19961b5a07cf1cd8c7d9ba1d55a0fddddbcadbec` | AsyncAPI → generated TypeScript |

## Workspace and toolchain model

- Root package is `pixel-agents`; npm workspaces are `server` and `webview-ui`. `package-lock.json` is lockfileVersion 3 and records the root plus both workspaces.
- Root engines declare Node `>=20`; `.nvmrc` selects Node 22. CI includes a dedicated Node 20 package smoke job and uses `.nvmrc` for the main and E2E jobs.
- Root TypeScript project checks `adapters/vscode`, `server/src`, and `core/src`; it excludes webview, scripts, dist and generated output directories. The webview has separate app/node TypeScript projects; its build runs `tsc -b` before Vite.
- Server and webview workspaces have their own Vitest configurations. Root test orchestration runs webview tests, server tests and Node's package-contract test suite.

## Build graph

1. **Protocol generation:** `npm run asyncapi:generate` invokes `tsx scripts/generate-messages.ts`; the generator reads `core/asyncapi.yaml` and writes `core/src/messages.ts`. The generated file is committed and must not be edited manually. `npm run asyncapi:validate` separately parses the AsyncAPI document.
2. **Static checks:** `npm run check-types` runs root `tsc --noEmit` and `tsc --noEmit -p server/tsconfig.test.json`; `npm run lint` runs ESLint over adapter/server/core and then webview.
3. **Extension/server bundle:** `node esbuild.js` bundles `adapters/vscode/extension.ts` to `dist/extension.js`, with VS Code as external. It also creates `dist/cli.js`, `dist/uninstall.js`, hook scripts under `dist/hooks/`, and a development PTY-host bundle `dist/pty-host.js`. CLI bundling leaves Fastify plugins and native PTY modules external. Asset files are copied from `webview-ui/public/assets` to `dist/assets`.
4. **Webview bundle:** `webview-ui` runs `tsc -b && vite build`, writing to `dist/webview`. The Vite plugin imports core asset build/loader functions, serves dev-only mock endpoints, and writes `furniture-catalog.json` and `asset-index.json` sidecars into the configured output's assets directory.
5. **Combined commands:** `compile` runs generation → type checks → lint → non-production esbuild → webview build. `package` runs generation → checks → lint → production esbuild → webview build. `build` aliases `compile`. `build:extension` and `build:webview` are separate commands and do not by themselves perform the full combined checks/generation sequence.
6. **Package lifecycle:** root `prepack` runs `npm run package`; `vscode:prepublish` also runs `npm run package`. Consequently, a normal npm pack/VSIX packaging path may execute build lifecycle scripts. CI's explicit `verify:npm-package -- --skip-build` path inserts npm `--ignore-scripts` for pack after its own explicit build; the release workflow's regular `verify:npm-package` runs the package lifecycle in the unprivileged build job.

## Distribution boundary

The root manifest's `files` allowlist includes release metadata, `dist/cli.js`, `dist/extension.js`, hook bundles, `dist/assets/`, `dist/webview/`, and the icon. It excludes source workspaces, scripts, docs and test artifacts. The development `dist/pty-host.js` is built by esbuild but deliberately not included in the package allowlist. Runtime external packages must therefore be declared as root production/optional dependencies when not bundled: Fastify and its plugins are root dependencies; `@lydell/node-pty` is optional and remains external. The package contract helper documents this reason and validates required/forbidden tarball paths.

## Integrated CI relation

The CI workflow validates AsyncAPI, checks generated-message drift and E2E inventory drift, performs typecheck/lint/webview tests/format check, builds extension and webview, verifies the npm package, runs server tests, audits dependencies, and runs a 3-OS × 3-shard Playwright E2E matrix. The release workflow on the remediation branch creates a verified artifact in a no-publish build job, then has separate VSIX and npm publisher jobs. See [CI/CD](CI-CD.md) and [GitHub CI deep audit](../github-ci/DEEP-AUDIT.md).

## Static observations and open verification

- Root `pngjs` is a devDependency even though core's PNG decoder imports it. The static esbuild configuration does not list `pngjs` as external, and the package contract states that the CLI/extension bundles carry this dependency. This makes a missing production dependency **not established by the current static evidence**; actual bundle/package contents remain unverified until the build and package smoke run.
- The webview Vite config imports Node-only core asset loader/build modules. This is build-tool execution, not evidence those Node modules enter browser runtime. Confirm final bundle composition before changing the core/browser boundary.
- `build:extension` creates several outputs beyond the VS Code extension entry; the package allowlist, not the existence of a build output, determines what is published.
- Hook bundles target Node 18 syntax while package engines require Node >=20; this is a transpilation target, not proof the overall package supports Node 18.
- No generated artifacts, tarballs, VSIX, bundle metafiles or output directory were inspected in this session.

## Execution boundary

Static manifest/config/workflow inspection only. No `npm ci`, generation, typecheck, lint, build, `npm pack`, VSIX package, test or runtime operation was executed. No source or dependency was changed.

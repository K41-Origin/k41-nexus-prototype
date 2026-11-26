# System Dependency Map

**Status:** STATIC MAP IN PROGRESS — major compile-time, host composition, protocol, persistence and trust-boundary edges are SOURCE-VERIFIED; npm lockfile transitive closure is mapped (GOV-DEPMAP-002); source import inventory across 334 code modules and terminal/process ownership are statically mapped (GOV-DEPMAP-003/004). Bundle/metafile comparison, symbol-level call graph and runtime lifecycle remain OPEN.

**Application/source snapshot:** `main@b1104a808cf70382c2bd14a94d372d907a500cda`; tree `97940dabd84264b6281917b11928ebae6156752c`, 933 entries, `truncated=false`. Current verification: `main@2e35f6df56d32a528c13a9d2f5d65e5db470c644`; tree `dc626a57bc6e0afe64a1dae78e90302adf99af47`, 933 entries, `truncated=false`; all 425 source-tree blobs under the audited roots are unchanged from the source snapshot.  
**Workflow remediation snapshot:** `security/remediate-build-008-009@1b71acd07b48d56527d0ad72e4d5e23713b390a7`; PR #2 diff against main contains only `.github/workflows/ci.yml` and `.github/workflows/publish-extension.yml`.  
**Dependency graph:** npm lockfile closure mapped in GOV-DEPMAP-002; source import graph statically inventoried across 334 code modules in GOV-DEPMAP-003; symbol-level call paths, bundle reachability and runtime lifecycle remain OPEN.

This is the single owner for cross-module edges. Module deep audits continue to own detailed findings; this map references rather than duplicates them.

## Evidence labels

- **SOURCE-VERIFIED:** implementation file fetched at the source snapshot; path, line range and blob SHA recorded.
- **DOC-RECORDED:** edge/behavior is recorded in an existing module technical document; this pass reused it without repeating unchanged source review.
- **TREE-VERIFIED:** path exists in the complete source tree.
- **INFERRED:** conclusion follows from declared/configured structure but is not confirmed by built artifact or runtime.
- **OPEN:** runtime or missing edge is not established.
- No TEST-EXECUTED or RUNTIME-REPRODUCED evidence was produced in this mapping pass.

## 1. Compile-time and build/package edges

| From → To | Edge kind | Evidence | Status / notes |
|---|---|---|---|
| `core/asyncapi.yaml` → `scripts/generate-messages.ts` → `core/src/messages.ts` | Generator/configuration | `scripts/generate-messages.ts:1-18,30-45`, blob `19961b5a07cf1cd8c7d9ba1d55a0fddddbcadbec`; `package.json:92-106`, blob `66efe28fd2927466c5f8283c6e26fe63da427079` | SOURCE-VERIFIED. YAML is authoritative; generated TS must not be edited manually. |
| `adapters/vscode/extension.ts` → `server/**` + `core/**` | Static import / composition | `adapters/vscode/extension.ts:1-12`, blob `6877b076b5a9f7117f78985a4923d19bf5f03b45`; adapter module record | SOURCE-VERIFIED / DOC-RECORDED. Adapter directly imports concrete server services, not only a narrow host facade. |
| `esbuild.js` → extension, CLI, hooks, PTY host, uninstall outputs | Build configuration | `esbuild.js:19-32,41-54,80-95,127-175`, blob `450d0d3a1e2e194d47d5bb598c1669b9f3c33955` | SOURCE-VERIFIED. VS Code API and selected CLI runtime packages are external; assets are copied to dist. |
| `webview-ui/vite.config.ts` → `core/src/assets/**` → `dist/webview/assets/*.json` | Build-time import/output | `webview-ui/vite.config.ts:8-14,27-33,50-65,99-108,113-125`, blob `b47531773bf6fea57fe6d46a6291133371aa56ea` | SOURCE-VERIFIED. Node asset decode/build helpers are imported by Vite config; do not mistake this for browser runtime import. |
| Root package manifest + lockfile → npm package allowlist and external runtime dependencies | Package/configuration | `package.json:12-15,92-108,127-180`, blob `66efe28fd2927466c5f8283c6e26fe63da427079`; `package-lock.json`, blob `e183f6eced2e598496010a79c56a19f32e3dbe83`; package-contract helper blob `329175a73e3bb2653dd7d2ada401492ecf3b5d4a` | SOURCE-VERIFIED. Lockfile v3, 816 package records. Fastify remains external and root production dependency; PTY is optional/external. Actual tarball contents remain OPEN until package execution. |

## 2. Runtime composition and host ownership

### VS Code embedded host

`extension.ts` constructs `FileStateAdapter(namespace: vscode)`, performs legacy VS Code state migration, creates `PixelAgentsViewProvider`, and registers it as the webview view provider. SOURCE-VERIFIED: `adapters/vscode/extension.ts:1-42`, blob `6877b076b5a9f7117f78985a4923d19bf5f03b45`.

The view provider creates/owns:
- `AgentStateStore` and its `StateAdapter`;
- `AgentRuntime`;
- `PixelAgentsServer`;
- host terminal adapter, workspace directory resolver, file watchers and webview broadcast buffer.

It wires server hook events to `runtime.handleHookEvent` and starts an embedded server with the store. SOURCE-VERIFIED: `adapters/vscode/PixelAgentsViewProvider.ts:267-285`, blob `94d1ab2e1427377e67ff430a852d49863e5e4d87`; construction/ownership is further DOC-RECORDED in [Adapters DEEP-AUDIT](adapters/DEEP-AUDIT.md) and [Server Runtime Lifecycle](server/RUNTIME-LIFECYCLE.md).

### Standalone host

`server/src/cli.ts` builds the asset cache, creates `AgentStateStore`, `FileStateAdapter(namespace: standalone)`, `PixelAgentsServer`, PTY manager and `AgentRuntime`, then wires hook callbacks and starts the server. SOURCE-VERIFIED: `server/src/cli.ts:301-339`, blob `2ab60f30b17b856d17c68435337663aab000b7e7`.

### Server transport composition

`PixelAgentsServer.start()` selects/reuses a compatible embedded or standalone instance, then calls `createHttpServer`. The HTTP server registers health, hook, usage, WebSocket and terminal routes, listens, and returns the bound port. SOURCE-VERIFIED: `server/src/server.ts:90-138`, blob `e3616e30f635ce5074a62829cd518af455b24009`; `server/src/httpServer.ts:91-137`, blob `ad5104a14233aa29834e008833a0f21458fc887b`.

## 3. Client/server protocol and transport edges

| From → To | Edge kind | Evidence | Status / notes |
|---|---|---|---|
| AsyncAPI YAML → generated discriminated unions | Contract/generation | `core/asyncapi.yaml` blob `ff1247118819fe68852da11996c660df1726aa66`; `core/src/messages.ts` blob `2fd177f12327c03ae5bdbc4beb83360121c5f703`; generator above | SOURCE-VERIFIED. |
| Webview → VS Code host | MessageTransport / postMessage | `webview-ui/src/transport/index.ts:1-53`, blob `066cb0b06dd1c9a00c74dda27a21fe0c49e1b14a`; `postMessageTransport.ts:15-31`, blob `f9b622f6bc28eb18b3bb7131e10c596ad7e68acd` | SOURCE-VERIFIED. Transport factory selects PostMessage when not browser runtime. |
| Standalone browser → `/ws` | MessageTransport / WebSocket | `webview-ui/src/transport/index.ts:8-49`, blob `066cb0b06dd1c9a00c74dda27a21fe0c49e1b14a`; `webSocketTransport.ts:16-85`, blob `33008b95327f9e2a18e23aaf0f2060416c95aba9` | SOURCE-VERIFIED. Reconnect and queue behavior/finding WV-RESYNC-02 remain documented in [UI Transport](webview-ui/TRANSPORT.md). |
| `/ws` → `handleClientMessage` → store/runtime/side effects | Runtime call | `server/src/httpServer.ts:190-285`, blob `ad5104a14233aa29834e008833a0f21458fc887b`; `server/src/clientMessageHandler.ts:156-195`, blob `d7ae8235ee75de63b1716a4b67f27d69023f5d96` | SOURCE-VERIFIED. The top-level privilege gate refuses every non-`webviewReady` message for unprivileged clients. |
| Store events → WebSocket messages | Event producer/consumer | `server/src/httpServer.ts:190-245`; `server/src/agentMessages.ts:1-24`, blob `df521f7a6d9ddbad8d7ee0c567df4054c500b9ea` | SOURCE-VERIFIED. AgentAdded/Removed and broadcast events are mapped to control-plane messages. |
| Raw terminal stream | Separate data plane | `core/src/terminalFrames.ts` and terminal routes; [Core Protocol](core/PROTOCOL.md), [HTTP/WebSocket](server/HTTP-WEBSOCKET.md) | DOC-RECORDED. Terminal frames are not ordinary AsyncAPI ClientMessage/ServerMessage events. |

## 4. Provider and hook flow

1. `core/src/provider.ts` defines the normalized `HookProvider` interface and provider event contract; blob `03c25878187aeeea8e710b1abde82c7efaab5ec9`.
2. `server/src/providers/index.ts` registers `hookProviders = [claudeProvider]` and resolves IDs; blob `005d099c09f47b19f36e41c87bc1cb1af27f8c47`, lines 20-29.
3. Provider hook scripts POST to `/api/hooks/:providerId`; HTTP route authenticates and forwards normalized events to the `PixelAgentsServer` callback; [Server Runtime Lifecycle](server/RUNTIME-LIFECYCLE.md) and [HTTP/WebSocket](server/HTTP-WEBSOCKET.md) are DOC-RECORDED. The adapter/CLI callback reaches `AgentRuntime.handleHookEvent`.
4. Consent ask is gated by installed state, durable answer, preference and privileged connection: `server/src/providers/hook/consentGate.ts:11-43`, blob `858a83230bdeac95d3858a3b4c1134de96ae83e8`.
5. Consent response is resolved against registered provider IDs and sent to a process-wide serialized executor: `server/src/clientMessageHandler.ts:388-399`, blob `d7ae8235ee75de63b1716a4b67f27d69023f5d96`; `consentExecutor.ts:49-95`, blob `523dbde800d275c8f070a7d44ac48956e4034b63`.

**Boundary:** the current provider registry contains only Claude. `TeamProvider`/subagent events represent CLI team capabilities, not a general-purpose LLM worker scheduler. No outbound model/provider inference SDK or generic Main Agent orchestrator was identified in the audited server dependency inventory.

## 5. Persistence ownership

| Data | Adapter / owner | Evidence |
|---|---|---|
| Agent state, seats, card order | `StateAdapter` contract → `FileStateAdapter` namespace-scoped files | `core/src/adapter.ts:15-35`, blob `af34de4cd31eaa0abff4298968b36c5a97de701f`; `server/src/fileStateAdapter.ts:47-102`, blob `62051790826ff7287d21dca93f42e5a86dc41908` |
| Per-host preferences | `FileStateAdapter` + shared config persistence namespace | Same source; [Server Persistence](server/PERSISTENCE.md) |
| Layout, characters, wardrobe, directories, consent and server registry | Server persistence services under `~/.pixel-agents` plus provider-owned external Claude config/transcripts | DOC-RECORDED in [Server Persistence](server/PERSISTENCE.md) |
| VS Code legacy state migration | Adapter migration from `workspaceState/globalState` into file-backed adapter | DOC-RECORDED in [Adapters DEEP-AUDIT](adapters/DEEP-AUDIT.md) |

Persistence has both shared machine-level data and host-scoped agent/seat state. Runtime consistency, multi-process races and migration completion remain bounded by existing server findings F-01–F-09 and adapter findings A-01–A-10.

## 6. Terminal/process execution and trust boundaries

- VS Code terminal lifecycle is adapter-owned through `VscodeTerminalAdapter`; standalone terminal launch is server-owned through PTY manager. Embedded server starts without a server-side PTY manager; standalone passes one into HTTP and terminal routes.
- Native PTY module is optional and external; missing PTY support degrades terminal launch rather than removing the office UI. DOC-RECORDED in [Server Dependencies](server/DEPENDENCIES.md).
- Standalone WebSocket connection acceptance and privilege are separate checks. `wsAuth.ts` documents same-origin/Host/token predicates; `clientMessageHandler.ts:164-170` is the fail-closed read-only command gate. SOURCE-VERIFIED.
- Binding standalone to non-loopback HTTP exposes an unencrypted network surface; remote TLS/auth/device registration requirements in the future hybrid-engine direction are not implemented as a general remote-client security layer. DOC-RECORDED in [HTTP/WebSocket](server/HTTP-WEBSOCKET.md).

## 7. Infrastructure and release edges

- `package.json` → `npm ci` → generation/checks/build/test/package scripts; [Build System](infrastructure/BUILD-SYSTEM.md) is SOURCE-VERIFIED static config.
- `.github/workflows/ci.yml` consumes those scripts and uploads test artifacts; blob `8dc8cdf30e2cb1857708a8bd601807c08df34368` on remediation branch.
- `.github/workflows/publish-extension.yml` has unprivileged build/verify and separate VSIX/npm publishers; blob `0b408f04d6b7c47d1405b9259d257fd21bdcb15d`. BUILD-008/009 remain OPEN pending merge and workflow/settings verification.
- GitHub Environment, repository/organization rulesets, secret scopes, npm trusted-publisher tuple and external registry outcomes are not available from source tree evidence; OPEN.

## 8. Current-state dependency summary

```text
                    core/ (contracts, schemas, domain + asset helpers)
                     ^              ^                    ^
                     |              |                    |
          adapters/vscode       server/              webview-ui/
             |   |                 |                    |
             |   +-- StateAdapter  +-- AgentRuntime     +-- MessageTransport
             |                     |                    |
             +-- PixelAgentsViewProvider                 +-- postMessage (VS Code)
                    |                                    +-- WebSocket (/ws)
                    +-- PixelAgentsServer <--- standalone CLI
                              |
                         httpServer/Fastify
                         /      |       \
                    hook API   /ws    terminal WS
                       |
                 HookProvider registry
                   (Claude only)
```

The diagram is a structural summary, not a complete call graph or runtime proof.



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

## 9. Known gaps and continuation (superseded by GOV-DEPMAP-003/004)

The source import inventory and terminal/process ownership work previously listed as pending are now recorded in §§10–11. Remaining work is:

1. Compare the mapped source import graph with esbuild/Vite metafiles and actual npm/VSIX outputs.
2. Verify generated protocol parity and package contents only after authorized build/package execution.
3. Verify multi-window/multi-process persistence, runtime teardown and reconnect behavior dynamically; preserve F-01–F-09, A-01–A-10 and WV lifecycle findings.
4. Verify real network/auth/TLS/proxy behavior and registry/Environment policy only with authorized runtime/settings access.
5. Keep dynamic edges OPEN until observed. Do not infer lifecycle correctness from constructor wiring.

No tests, build, typecheck, lint, generation, package, CI dispatch, network experiment, PTY experiment or runtime reproduction was performed. No application source changed.


## 10. Source import graph — GOV-DEPMAP-003 (2026-11-26)

**Coverage:** the complete recursive tree at `main@2e35f6df56d32a528c13a9d2f5d65e5db470c644` (tree `dc626a57bc6e0afe64a1dae78e90302adf99af47`) was not truncated. Static imports/exports and literal dynamic-import/require edges were inventoried from all 334 TypeScript/JavaScript source modules in the application/tooling roots:

| Root | Code modules fetched |
|---|---:|
| `adapters/` | 8 |
| `core/src/` | 27 |
| `server/src/` | 74 |
| `webview-ui/src/` | 214 |
| `scripts/` | 11 |
| **Total** | **334** |

Build/test entry configuration and manifests were additionally fetched for esbuild, Vite, Vitest, Playwright, TypeScript and npm. All 425 tracked blobs under `core/src/`, `server/src/`, `webview-ui/src/`, `adapters/` and `scripts/` match the prior source tree at `main@b1104a808cf70382c2bd14a94d372d907a500cda`: zero additions, removals or changed SHAs. This reuses historical source review evidence; unchanged files were not treated as newly re-reviewed for their behavior.

**Layer edges established:**

- `core/src/` is the lowest shared source layer. Its imports remain internal to core plus Node facilities and `pngjs`; no upward source imports into `server/`, `webview-ui/` or `adapters/` were found in the fetched core modules.
- `server/src/` imports core contracts/domain/asset helpers, Node built-ins and server-local services. Its `vscode` references in the existing server type/watcher boundary are type-level adapter contracts, not evidence of a browser/server runtime import.
- `adapters/vscode/` composes VS Code API with core contracts and concrete server runtime/persistence/provider services. This is a direct source dependency on server implementation, not only a host facade.
- `webview-ui/src/` imports core contracts/pure domain helpers, React/React DOM, xterm and webview-local UI/office/transport modules. No direct source import from `server/` or `adapters/` was found; communication remains through postMessage or WebSocket transports.
- `scripts/` is Node/tooling-oriented and imports Node built-ins plus AsyncAPI/parser/modelina, semver and package/test utilities as applicable.

**Important transitive boundary:** `webview-ui/vite.config.ts` imports `core/src/assets/build.ts` and `core/src/assets/loader.ts` in the Node build process. The core asset loader reaches `pngDecoder.ts` and `pngjs`. This is a build-time Node dependency and is not proof that `pngjs` or filesystem modules enter the browser bundle. The standalone server's asset loader also reaches the core PNG decoder at server runtime. The final Vite/esbuild bundles have not been inspected.

**External/runtime dependency families observed:** React/React DOM; xterm browser and headless/serialize packages; Fastify and its CORS/static/WebSocket plugins; AsyncAPI modelina/parser; Vite/React/Tailwind/Vitest/Allure/Playwright tooling; Node built-ins; and the VS Code host API. Native PTY packages are a special case: `ptyModule.ts` resolves `@lydell/node-pty` then `node-pty` through a variable `require(id)`, so this edge is not a normal static import and must be checked against the lockfile/package platform matrix. The npm lockfile closure itself remains the completed GOV-DEPMAP-002 evidence.

**Limits:** this is a static source import inventory and layer map, not a compiler AST call graph, bundler metafile, tree-shaking/reachability report, SBOM, tarball/VSIX inspection or runtime dependency proof. Variable-based runtime lookup and platform-specific native install behavior remain separately bounded.

## 11. Terminal and process ownership — GOV-DEPMAP-004 (2026-11-26)

### VS Code embedded surface

- `adapters/vscode/agentManager.ts:60-104` creates a `vscode.window.createTerminal`, optionally shows it and sends the provider launch command with `terminal.sendText`. VS Code owns the terminal process lifecycle.
- `adapters/vscode/vscodeTerminalAdapter.ts:1-14` only exposes VS Code's active/all terminal handles through the core adapter contract; it does not spawn or kill the process itself.
- The embedded server does not receive a PTY manager; terminal routes are not registered for the embedded host. Adapter/server cleanup relationships remain subject to existing A-03/A-05/A-06 and F-03/F-05 findings.

### Standalone default — in-process PTY

- `server/src/cli.ts:230-266,301-330,511-533` selects a disabled manager for `--no-terminal`, a `PtySessionManager` by default, or the development host mode; it injects the selected `TerminalSessions` implementation into `PixelAgentsServer`.
- `PtySessionManager.create` calls the dynamically resolved PTY module's `spawn` once per agent; `PtySession` mirrors output through headless xterm and owns exit/data listeners. `dispose` sends the default termination signal then escalates to SIGKILL after the grace period; `disposeAll` waits for exits or its bounded backstop.
- `terminalRoutes.ts:49-92,98-159` authenticates and attaches only to an existing session. Terminal socket close removes listeners only; it does not kill the PTY. Agent close/process exit and server shutdown are the process-ending paths.
- CLI graceful shutdown awaits `ptyManager.shutdown()`; in-process PTYs are terminated before runtime disposal. The server's own `PixelAgentsServer.stop()` still calls Fastify close without awaiting it (existing F-05 boundary).

### Development-only detached PTY host

- `--pty-host` selects `RemotePtySessions`; if no host socket is listening, `startPtyHostProcess` starts `node dist/pty-host.js` with `detached: true`, log-file stdio, then `unref()`. The host runs the same `PtySessionManager` and owns the actual PTY children.
- The server communicates over a local Unix-domain socket with a versioned line-JSON protocol. `RemotePtySessions.shutdown()` detaches its session listeners and ends the socket; it intentionally leaves PTYs alive. A subsequent server reconnects and reattaches survivors. Loss of the host process ends the remote sessions; host SIGTERM/SIGINT closes its listener and disposes every PTY.
- The host has an idle-exit timer and is a development-only output; `dist/pty-host.js` is excluded from the published package allowlist according to the existing build/package map.

**Evidence:** `agentManager.ts` blob `30020b3e34d2d83fa4536e0d27f55c9e4fe3295c`; `vscodeTerminalAdapter.ts` `b7f7b6019655d76a0c03ed95a24a52e1a0a18b18`; `cli.ts` `2ab60f30b17b856d17c68435337663aab000b7e7`; `ptySessionManager.ts` `f48f6de4fb552e52d21402d7bf5db5e712eba550`; `ptyModule.ts` `958db26ea120353e8d3dea01b794fbf8a2f1c875`; `remotePtySessions.ts` `022c4df100edafa7dedbf397d2b099d3d14e7dab`; `ptyHost.ts` `007b00c81d0acaeb61cee2ae79f23103866aaa60`; `ptyHostMain.ts` `5be0b6debf80e2c292a25752252611d1f048b388`; `terminalRoutes.ts` `434ac1db106d593577409b42e872331ac54e4323`; `terminalSessions.ts` `f01b0343624eb05b2608619fedfef16c53f766f3`; `httpServer.ts` `ad5104a14233aa29834e008833a0f21458fc887b`; `server.ts` `e3616e30f635ce5074a62829cd518af455b24009`.

**Verification boundary:** SOURCE-VERIFIED static ownership/call edges only. No PTY process, terminal socket, detach/reattach, host crash, signal or shutdown experiment was run. Preserve existing lifecycle findings; do not mark dynamic ownership/recovery RUNTIME-REPRODUCED.

## 12. Continuation and remaining gaps

1. Compare the source import map with actual esbuild/Vite metafiles and produced npm/VSIX contents; establish which external packages remain runtime requirements and which code is bundled.
2. Complete symbol-level call-path questions only where a named cross-module behavior requires them; do not expand into a redundant whole-repository function graph.
3. Verify native PTY resolution/install/spawn across supported OS/Node/ABI combinations and test both in-process and detached-host lifecycle when execution is authorized.
4. Preserve F-01–F-09, A-01–A-10, WV-RESYNC-01/02 and existing protocol/security findings. Verify multi-process persistence, teardown, hook delivery, auth/TLS/proxy and registry settings dynamically.
5. Inspect generated outputs, bundle metadata, SBOM/licenses, tarball and VSIX only after authorized build/package execution.

No tests, build, typecheck, lint, generation, package, CI dispatch, network experiment, PTY experiment or runtime reproduction was performed in this mapping pass. No application source changed.

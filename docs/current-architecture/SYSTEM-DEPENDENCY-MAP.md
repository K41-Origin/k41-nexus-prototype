# System Dependency Map

**Status:** STATIC MAP IN PROGRESS — major compile-time, host composition, protocol, persistence and trust-boundary edges are SOURCE-VERIFIED; npm lockfile transitive closure is mapped (GOV-DEPMAP-002). Exhaustive source import/call graph and runtime lifecycle remain OPEN.

**Source snapshot:** `K41-Origin/k41-nexus-prototype`, branch `security/remediate-build-008-009`, commit `86d3b1dbddae7605e5e2092886c12982f8e4bf58`.  
**Base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Tree:** 856 entries, `truncated=false`; complete tree comparison against base found exactly two changed workflow blobs (`.github/workflows/ci.yml`, `.github/workflows/publish-extension.yml`). Application source and build manifests are unchanged from the prior main baseline.

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

## 9. Known gaps and continuation

1. Build the exhaustive source import/call graph (including script/config imports) and compare it to bundler output/metafiles; the npm lockfile transitive resolution graph is now mapped in infrastructure/DEPENDENCIES.md.
2. Trace all server route registrations and all terminal process ownership edges where not already covered by module deep audits.
3. Verify actual generated protocol parity and built npm/VSIX contents only when execution is authorized.
4. Clarify multi-window/multi-process persistence ownership and runtime teardown; preserve F-01–F-09, A-01–A-10 and WV lifecycle findings.
5. Verify real network/auth/TLS/proxy behavior and registry/Environment policy only with authorized runtime/settings access.
6. Keep all dynamic edges OPEN until observed. Do not infer lifecycle correctness from constructor wiring.

No tests, build, typecheck, lint, generation, package, CI dispatch, network experiment, PTY experiment or runtime reproduction was performed. No application source changed.

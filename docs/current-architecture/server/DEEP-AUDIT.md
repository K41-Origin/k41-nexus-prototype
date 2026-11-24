# Server Deep Audit Findings

**Baseline:** `f1af3e0c36573e4317143969cf43dde642877ba6`  
**Inventory:** 135 tracked blobs; recursive tree not truncated.  
**Coverage:** 74/74 runtime source files fetched/reviewed; 57/57 test/support files fetched and their suites/cases inventoried; 4/4 root support/config files fetched/reviewed.  
**Execution:** No build, test suite, real Claude Code session, live network security test, or OS PTY matrix was executed. This is a static source audit, not runtime verification.

## 1. Actual server role

The current server is a Node.js host for observing and integrating external coding-agent sessions. It is not a general-purpose LLM inference engine, model gateway, planner, or Main Agent/Worker orchestrator.

Major data paths:

1. Provider hook script → authenticated HTTP hook endpoint → provider normalization → session routing → HookEventHandler → AgentStateStore → WebSocket clients.
2. Provider session roots → workspace/global scanners → JSONL polling → transcript parser → tool/context/team/background-spawn state.
3. Browser/VS Code WebSocket → clientMessageHandler → settings, consent, persistence, directories, assets, conversations, launch/close.
4. Privileged terminal WebSocket → TerminalSessions → native PTY or development PTY host → numbered output stream and serialized screen replay.

Persistence is split across machine-wide config/profiles/layout/conversation/usage, adapter-namespaced agent state, provider-owned settings/transcripts, and optional live PTY process state.

## 2. Findings

Severity is an engineering priority. Runtime impact remains to be verified.

### F-01 — Module-global runtime state undermines instance isolation
**Priority: High | Files:** `agentRuntime.ts`, `fileWatcher.ts`, `transcriptParser.ts`, `paletteAssigner.ts`, `usageLimits.ts`

Runtime construction registers providers and callbacks through module-level setters. `fileWatcher.ts` retains `trackedProjectDirs`, `knownTeammateFiles`, and `clearDetectionDeps` globally. `transcriptParser.ts` retains its provider and callbacks globally. `AgentRuntime.stop()` stops local timers/watchers and disposes its router/shadow watch, but does not unregister/reset these module-level registrations and sets. `ensureProjectScan()` captures `clearDetectionDeps` only on its first call.

**Impact:** another runtime in the same process, tests sharing a module graph, hot reload, or future multi-provider hosting can inherit stale closures/state.

**Direction:** move providers, scan registries, callbacks and clear-detection dependencies into an explicit runtime-scoped dependency object. Add deterministic disposal/unregistration.

### F-02 — /clear fallback captures only the first project root
**Priority: High | File:** `fileWatcher.ts`

`ensureProjectScan()` registers multiple roots, but initializes `clearDetectionDeps.projectDir` only once. Per-agent /clear detection later searches only that captured directory.

**Impact:** /clear detection for an agent in a later workspace root may search the wrong directory even though regular project and external scanners are multi-root.

**Direction:** derive the search directory from the active agent's `projectDir`, or maintain per-root dependencies. Add a two-root regression test.

### F-03 — Central agent removal does not unregister its session route
**Priority: Medium | Files:** `agentRuntime.ts`, `hookEventHandler.ts`, `sessionRouter.ts`, `fileWatcher.ts`

`AgentRuntime.removeAgent()` stops watchers, calls adapter cleanup, removes the store entry and persists, but does not unregister the session ID from `SessionRouter`. Some callers unregister explicitly; generic stale-file removal and some close paths call `removeAgent` directly.

**Impact:** stale session→agent mappings remain after an agent disappears. Monotonic IDs and later re-registration reduce wrong-agent risk, but stale routing state and lifecycle ownership are real.

**Direction:** make unregistration part of central removal, with explicit exceptions only where justified. Test every removal source.

### F-04 — Provider identity is dropped before event normalization
**Priority: Medium; future-provider blocker | Files:** `httpServer.ts`, `server.ts`, `agentRuntime.ts`, `hookEventHandler.ts`, `providers/index.ts`

The hook endpoint accepts any syntactically valid provider ID and forwards it. `AgentRuntime.handleHookEvent(providerId, event)` passes the ID, but `HookEventHandler.handleEvent(_providerId, event)` ignores it and normalizes with the single provider bound to the handler. Only Claude is registered today, so this is a latent extension boundary. Usage ingress does resolve provider IDs, making the two paths inconsistent.

**Direction:** resolve/reject provider IDs at ingress and route through the matching provider runtime. Adding a provider only to `hookProviders` is insufficient.

### F-05 — HTTP server close is initiated but not awaited
**Priority: Medium | Files:** `server.ts`, `cli.ts`

`PixelAgentsServer.stop()` invokes `app.close()` without awaiting/returning its promise. CLI shutdown then calls `process.exit(0)`.

**Impact:** Fastify/WebSocket close hooks are not proven complete before process exit.

**Direction:** make stop async, await `app.close()`, and await it from all hosts. Test with an async close hook and connected socket.

### F-06 — Existing standalone token permissions are not tightened
**Priority: Medium | File:** `serverToken.ts`

New/rotated tokens are written with mode `0600`, but an existing usable token is returned without checking/correcting its mode.

**Impact:** a token created by an older version, copied, or manually made permissive may remain broadly readable.

**Direction:** inspect and tighten existing token-file permissions where supported; test an existing permissive file and document platform behavior.

### F-07 — Exceptional synchronous JSONL reads can leak file descriptors
**Priority: Low–Medium | File:** `fileWatcher.ts`

Read paths open a descriptor, call `readSync`, then close it in the same try block. If read throws after open succeeds, catch runs before close.

**Impact:** repeated read races can leak descriptors.

**Direction:** use `try/finally` for every descriptor lifetime or FileHandle APIs; add fault-injected read tests.

### F-08 — Client message runtime validation is inconsistent
**Priority: Medium | Files:** `clientMessageHandler.ts`, `configPersistence.ts`, `directories.ts`, `characterProfilesPersistence.ts`

The top-level read-only gate is fail-closed and several features validate carefully. Other cases cast values from arbitrary JSON: general settings fields, `saveLayout`, `saveAreaMappings`, and raw `saveAgentSeats` persistence. `saveAreaMappings` checks only that the value is a non-null object before casting it to `Record<string, string[]>`; arrays and malformed nested values can be written (later config reads coerce them).

**Impact:** malformed privileged payloads can persist invalid shapes or cause UI/config inconsistency. A local token is an authorization boundary, not a substitute for schema validation.

**Direction:** define runtime schemas per message, validate nested fields before side effects, and return explicit rejection messages.

## 3. Other coupling observations

- `clientMessageHandler.ts` is the main command switch. It hardcodes Claude for launch/capability reporting; conversation selection chooses the first provider with title + launch support. This matches today's single-provider product but is not a generic provider router.
- `AgentRuntime` shares lifecycle wiring, but terminal ownership remains split between the VS Code adapter and `standaloneAgentLauncher.ts`.
- `fileWatcher.ts` combines JSONL tailing, workspace/global scans, /clear detection, session adoption/reassignment, teammate discovery, background-spawn classification and team-config removal. It is a high-coupling refactor candidate.
- `transcriptParser.ts` is not merely a parser: it mutates state, emits events, manages timers, links teams and handles background-spawn lifecycle.
- `AgentStateStore` owns the in-memory map, lifecycle events and persistence seam; runtime AgentState contains transient data beyond core persisted schemas.
- Background children are derived from lead state/sidecars. Named spawns may become teammate characters; unnamed spawns use a shadow store.
- PTY replay uses a serialized headless-xterm screen snapshot plus numbered output chunks, not a raw byte ring. PTY host is development-only and uses a local Unix socket.
- Atomic rename avoids torn-file reads but does not prevent lost updates across concurrent processes; see `PERSISTENCE.md`.
- Hook events provide low-latency lifecycle signals; JSONL polling remains necessary for detailed tool content/context. They are complementary paths.
- Core `AgentEvent` represents normalized external CLI events. It is not the future orchestration Task/Worker domain model.

## 4. Test coverage inventory

All 57 files under `server/__tests__/` were fetched and their suites/cases enumerated.

| Area | Coverage focus |
|---|---|
| Runtime/store/session | Foreign-session gate, restore compatibility, store events, session mapping/buffering |
| Hook/transcript/team | Normalization/dispatch, JSONL lifecycle, teams, spawn notices and sidecars |
| Scanner/persistence | Adoption/dismissal, offsets, namespace persistence, file watcher, config/profile migration |
| HTTP/security/consent | HTTP, WebSocket, token/Host/Origin gates, log redaction and consent |
| Standalone/PTY | CLI, launch/restore, PTY lifecycle, PTY host and terminal protocol |
| Product/support | Activity snapshots, diagnostics, assets, directories, conversations, character looks, usage, hook scripts/installers, migration and fixtures |

Test files and cases were inventoried; this does not mean every assertion was independently exhaustively reviewed or that tests pass. No test suite was run. Real Claude Code behavior and macOS/Linux/Windows behavior still need integration/platform verification.


## 6. Test audit — assertion inventory and evidentiary limits

**Review status:** 57/57 test/support paths in the server test tree have been retrieved and inventoried. The test files are not all equivalent: some are narrow unit tests, some exercise integrated handlers with temporary files and fake processes, and some spawn the compiled production bundle. A test name or assertion is evidence of intended behavior, not proof that the implementation is correct in production.

### 6.1 Coverage by test file

| Test file(s) | What the assertions establish | Important limit |
|---|---|---|
| `agentActivityResend.test.ts`, `agentDiagnostics.test.ts` | Activity resend/snapshot and diagnostics behavior | Does not establish correctness of all live-client timing/race conditions |
| `agentRuntime.test.ts`, `agentRuntime.restoreDirectoryName.test.ts`, `agentRuntime.restorePalette.test.ts` | Runtime session ownership gate, watch-all behavior, restore compatibility for directory names and palette/repeat | Does not prove isolation across multiple runtime instances; unique directories in tests avoid global scanner contamination rather than proving cleanup |
| `agentStateStore.test.ts`, `fileStateAdapter.test.ts` | Store CRUD/events/listener disposal, adapter persistence and namespace separation | Does not prove concurrent process write safety or complete runtime shutdown |
| `assetReload.test.ts`, `seatedFrame.test.ts` | Asset reload and legacy seated-frame decoding | Does not cover all packaged asset combinations or browser/node entry-point behavior |
| `backgroundAgents.test.ts`, `backgroundSpawns.test.ts`, `teamUtils.test.ts` | Background agent classification, task/spawn relationship, inline teammate selection | Provider-specific assumptions remain; real CLI event compatibility needs integration evidence |
| `characterProfiles.test.ts`, `characterProfilesPersistence.test.ts`, `characterRemap.test.ts`, `wardrobeItems.test.ts` | Profile shape/migration, persistence, remapping and wardrobe validation | Does not establish resilience against every malformed nested payload at the WebSocket boundary |
| `claude-hook.test.ts`, `claude-statusline.test.ts`, `claudeHookInstaller.test.ts`, `claudeSettingsOwnership.test.ts`, `statusLineInstaller.test.ts` | Hook fan-out/timeout/dead PID handling, statusline output preservation, idempotent install/uninstall, ownership predicate, backup and third-party preservation | Hook/statusline script tests depend on compiled `dist/hooks` artifacts and may skip if not built; skip is not a pass |
| `claudeTeamProvider.test.ts`, `transcriptParser.test.ts`, `hookEventHandler.test.ts`, `teamUtils.test.ts` | Team identity extraction, old/new teammate discovery, sidecar/config parsing, spawn notice parsing, hook lifecycle and event buffering | Fixture-driven parser coverage does not prove behavior against every version of live provider transcript formats |
| `cli.test.ts`, `server.test.ts` | CLI argument boundaries, bundled entrypoint behavior, server startup/health/hook endpoint/registry/token rotation/reuse/stop | Most production CLI subprocess tests require `dist/cli.js`; they are skipped when absent. Static inspection did not execute them |
| `clientMessageHandler.test.ts` | Large integration-style dispatch suite: privilege gate, read-only response behavior, handshake ordering, hook consent outcome, launch/close, terminal capability, directories, settings, card order, profiles and persistence | The suite validates many known message cases but cannot guarantee every discriminated message and every nested JSON field is schema-validated. This aligns with F-08 |
| `consentCopy.test.ts`, `consentFlow.test.ts`, `consentGate.test.ts` | Disclosure copy invariants, privilege gating, install/decline/not-now/revision paths, failed install/revert and serialization of concurrent answers | Uses controlled side effects; real external settings-file contention and user edits remain integration concerns |
| `contextUsage.test.ts`, `usageLimits.test.ts` | Context-window selection, snapshot behavior, transcript-tail parsing, provider usage persistence and malformed report handling | Model/window catalog correctness can drift as providers change; no live provider measurements were run |
| `conversations.test.ts`, `directorySuggestions.test.ts`, `directories.test.ts`, `directoryCharacters.test.ts`, `recentCharacters.test.ts` | Transcript title/cwd lookup, caching/recency, directory validation/deduplication, default character selection and roster recency | Filesystem fixtures do not cover all platform path semantics, permission errors or concurrent mutations |
| `coreGuards.test.ts`, `paletteAssigner.test.ts` | Runtime guards, palette range/assignment policy, non-mutation and default/external asset counts | Does not prove global palette dependency is isolated between independent runtimes; relevant to F-01 |
| `fileWatcherDismissal.test.ts`, `initialReadOffset.test.ts`, `jsonFileWatcher.test.ts` | Adoption/dismissal cooldowns, /clear two-tick behavior, offset selection, own-write suppression and watcher disposal | These tests exercise selected scanner state transitions, not every multi-root/global-state lifecycle combination; F-01/F-02 remain open |
| `hookEventHandler.test.ts`, `sessionRouter.test.ts` | Session mapping, pending/buffered events, resume/clear behavior, event dispatch and disposal of handler timers/maps | Router unit tests prove unregister itself works, not that every `AgentRuntime.removeAgent()` path invokes it; F-03 remains open |
| `httpServerLog.test.ts`, `httpServerWs.test.ts`, `wsAuth.test.ts` | Token redaction, origin/host/token checks, DNS-rebinding defense, allowed-host reverse-proxy behavior, privilege decisions and consent handshake | Mostly controlled request/handshake tests; does not constitute a live network penetration test |
| `migrateVsCodeState.test.ts`, `configPersistence.test.ts`, `characterProfilesPersistence.test.ts` | Legacy state migration, idempotency, namespace persistence, write-failure behavior and profile storage | Does not establish multi-process transactional consistency |
| `ptySessionManager.test.ts`, `ptyHost.test.ts`, `terminalFrames.test.ts`, `terminalRoutes.test.ts` | PTY spawn/kill/escalation, process exit, listeners, screen/output replay, protocol validation and terminal WebSocket authentication | Fake PTY and Unix-socket tests do not establish native PTY behavior on every supported OS; PTY host is not a substitute for a platform matrix |
| `standaloneAgentLauncher.test.ts` | Launch/resume command construction, id allocation rollback, PTY failure, exit cleanup and transcript watcher behavior | Provider launch behavior is mocked; actual CLI startup/session creation remains external integration |
| `paletteAssigner.test.ts`, `characterRemap.test.ts`, `seatedFrame.test.ts` | Character selection/remap/legacy asset behavior | Does not cover the complete asset pipeline or browser packaging |

### 6.2 Direct correlation with F-01–F-08

| Finding | Existing test evidence | Audit conclusion / missing regression |
|---|---|---|
| F-01 Global runtime state | Runtime, watcher, parser and palette tests cover local behavior; runtime tests isolate paths | **Not closed.** No demonstrated create→stop→create test with distinct provider callbacks, project roots and parser dependencies in one module graph. Add lifecycle isolation and stale-callback assertions. |
| F-02 First-root /clear dependency | Dismissal tests cover /clear pending/adoption transitions | **Not closed.** Add two-root test: initialize root A first, then process /clear for an agent in root B and assert B is searched. |
| F-03 Session route cleanup | SessionRouter test verifies unregister; runtime tests cover selected removals | **Not closed.** Add table-driven removal-path tests asserting session route is absent after every generic removal/close/stale-file path. |
| F-04 Provider identity | Provider-specific normalization/team tests and hook endpoint validation exist | **Not closed.** No evidence that two registered providers can receive hooks through ingress and normalize with their own provider implementation. Add provider A/B dispatch test and unknown-provider rejection. |
| F-05 Awaited shutdown | PTY manager tests cover process exit and dispose escalation; server tests cover stop metadata cleanup | **Not closed.** No assertion that `PixelAgentsServer.stop()` waits for Fastify close hooks and WebSocket closure before resolving/CLI exit. |
| F-06 Existing token mode | Server/CLI tests cover token creation/rotation and discovery | **Not closed.** No existing-token permissive-mode test asserting mode is tightened or a documented unsupported-platform outcome. |
| F-07 Descriptor closure | JSON watcher tests cover watcher disposal and failed writes | **Not closed.** No fault-injected synchronous JSONL read failure asserting descriptor closure after a successful open. |
| F-08 Message schema validation | Client handler tests cover read-only gate and selected malformed values; config/directory/profile tests cover feature-specific validation | **Partially covered only.** Existing tests do not provide exhaustive schema validation for every message. Add malformed nested payload matrices for layout, area mappings, seats and settings, asserting no persistence or side effects. |

### 6.3 Test harness quality and blind spots

1. **Compiled-artifact conditional skips:** `claude-hook.test.ts`, `claude-statusline.test.ts`, and the bundled CLI cases in `cli.test.ts` depend on generated `dist` files. CI/build ordering must be checked; a skipped case must be reported separately from passed cases.
2. **Mocks versus external behavior:** fake PTY modules, mocked provider runners, temporary home directories and fixture JSONL files give deterministic unit coverage. They do not prove compatibility with a live Claude Code installation, real terminal implementations, or external process behavior.
3. **Module-global state contamination:** several suites isolate HOME, use unique paths or reset state. Such isolation can mask cross-runtime state leakage unless a test intentionally shares the module graph and starts/stops multiple runtimes.
4. **Filesystem and OS assumptions:** Unix socket, file mode, symlink, rename and path behavior need execution on each supported OS. Static test inspection cannot establish platform pass status.
5. **Async completion:** tests using short settling timers or polling helpers cover expected completion paths, but the timeout itself is not proof that all pending work was drained. Shutdown and callback disposal need explicit awaited assertions.
6. **Security scope:** unit tests cover key Host/Origin/token branches, but do not replace live network testing through actual reverse proxies, browser behavior, DNS changes or hostile WebSocket clients.
7. **Coverage metrics:** no coverage report was generated. The inventory records named suites/cases and observed assertions, not line/branch coverage percentages.

### 6.4 Verification status

- Static retrieval/inventory: **57/57 test/support paths** present in the audited server test tree.
- Runtime source audit: **74/74 source files** previously reviewed.
- Test execution: **NOT RUN** in this audit session.
- Build/typecheck/protocol generation: **NOT RUN** in this audit session.
- Platform/integration testing: **NOT RUN**.
- No claim is made that the suite passes.
- The test audit does not close F-01–F-08. It identifies existing coverage and the specific missing regressions needed to verify those findings.

## 7. Exit criteria for the next repository audit stage

The server static audit is complete at the file-inventory/source-review level. Before implementation, the verification sequence in §5 remains required. The repository-wide audit can now proceed to `adapters/` while preserving F-01–F-08 as open findings. The next module audit must trace adapter ownership, activation/deactivation lifecycle, provider/session dependencies, message/data boundaries and teardown behavior, then correlate its tests and callers with the server findings.

## 5. Verification sequence before refactoring

1. Add regression tests for F-01–F-08.
2. Run the server test command and record exact results.
3. Run root typecheck/build and protocol-generation checks.
4. Exercise embedded/standalone startup, multiple roots/processes, hook fan-out, consent revision and shutdown/restart.
5. Run PTY tests on supported OSes, including missing/broken optional dependency and PTY-host reconnect.
6. Test token-file permissions, Host/Origin/DNS rebinding, reverse proxies, malformed WebSocket messages and terminal attach.
7. Convert verified findings into refactoring requirements only after the baseline is recorded.

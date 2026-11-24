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

## 5. Verification sequence before refactoring

1. Add regression tests for F-01–F-08.
2. Run the server test command and record exact results.
3. Run root typecheck/build and protocol-generation checks.
4. Exercise embedded/standalone startup, multiple roots/processes, hook fan-out, consent revision and shutdown/restart.
5. Run PTY tests on supported OSes, including missing/broken optional dependency and PTY-host reconnect.
6. Test token-file permissions, Host/Origin/DNS rebinding, reverse proxies, malformed WebSocket messages and terminal attach.
7. Convert verified findings into refactoring requirements only after the baseline is recorded.

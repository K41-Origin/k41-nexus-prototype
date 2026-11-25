# Adapters Deep Audit

**Baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Inventory:** 8/8 tracked blobs under `adapters/`; recursive tree not truncated.  
**Static coverage:** 8/8 files fetched and reviewed. `agentManager.ts` (556 lines) and `PixelAgentsViewProvider.ts` (1,230 lines) were read in line-range chunks.  
**Tests inspected:** `server/__tests__/migrateVsCodeState.test.ts`, `server/__tests__/fileStateAdapter.test.ts`.  
**Execution:** No tests, typecheck, lint, build, VS Code host, or live provider session was run.

## Findings

Severity is engineering priority, not confirmed exploitability.

### A-01 — Host registrations leak through module-global server setters
**Priority: High | Files:** `PixelAgentsViewProvider.ts`, `server/src/fileWatcher.ts`, `server/src/agentRuntime.ts`

The constructor calls `setTerminalAdapter(new VscodeTerminalAdapter())` and `setDirectoryNameResolver(...)`. These are module-global server dependencies. Provider disposal disposes runtime/watchers/store but does not reset these registrations.

**Impact:** extension host reload, tests, multiple runtimes, or another host in the same module graph can retain stale VS Code objects/closures. This concretely connects to server F-01.

**Direction:** runtime-scoped host capability injection and explicit unregister/disposal; test create→dispose→create in one process.

### A-02 — Webview controller is a high-coupling composition root
**Priority: High | File:** `PixelAgentsViewProvider.ts`

The 1,230-line class owns server/runtime/store construction, webview transport/buffering, hooks/consent, usage bridge, directories, settings, asset loading, layout, workspace/terminal events, launch/restore and teardown.

**Impact:** unrelated concerns share mutable state and lifecycle. This is a modularization seam, not proof that every concern needs a separate process.

**Direction:** identify application services/use cases and host ports; split by lifecycle/ownership, not line count.

### A-03 — Agent lifecycle ownership is split
**Priority: High | Files:** `agentManager.ts`, `PixelAgentsViewProvider.ts`, `server/src/agentRuntime.ts`

`agentManager.ts` launches/restores agents and owns watcher/timer setup; it also contains a local `removeAgent` helper duplicating cleanup in `AgentRuntime.removeAgent`. The provider owns terminal-close callbacks and explicitly unregisters sessions on some paths, while generic runtime removal does not enforce that invariant (server F-03).

**Direction:** define one lifecycle owner and explicit host effects such as terminal disposal and transcript dismissal. Centralize route unregister.

### A-04 — Launch is Claude-specific and can partially commit side effects
**Priority: Medium-High | File:** `agentManager.ts`

Launch uses Claude session-directory lookup, launch command, terminal prefix and session-marker logic. It creates/shows a terminal and sends the command before deriving projectDir and registering agent state. Later failures can leave a running but untracked terminal/session.

**Direction:** staged launch with compensating cleanup and provider launcher contract; fault-injection tests.

### A-05 — Event subscription ownership is unclear/incomplete
**Priority: Medium | File:** `PixelAgentsViewProvider.ts`

Workspace-folder listener is put in `context.subscriptions`. In `resolveWebviewView`, returned disposables from webview message and window active/closed terminal listeners are not added to a provider-owned disposable collection. Terminal listeners are not explicitly disposed in `dispose()`.

**Impact:** repeated view resolution may accumulate listeners or retain old references. Confirm actual VS Code view-resolution semantics dynamically.

**Direction:** own subscriptions in a disposable collection; dispose on view replacement and provider shutdown; test repeated resolve/dispose.

### A-06 — Restore grace-period timer is untracked
**Priority: Medium | File:** `agentManager.ts`

`restoreAgents` schedules a 10-second timeout to remove restored terminal agents with no processed data. Its handle is not in runtime timer maps and is not cancelled on provider/runtime disposal.

**Direction:** put timer under runtime lifecycle ownership; cancel on removal/disposal and test shutdown before timeout.

### A-07 — Migration clears Memento keys without awaiting updates
**Priority: Medium | File:** `migrateVsCodeState.ts`

After destination read-back succeeds, source keys are cleared using `void context.*State.update(..., undefined)`. Migration is synchronous and does not handle promise rejection in its pending/warning path.

**Direction:** async migration, await source cleanup, include rejection in retry/warning behavior.

### A-08 — Migration uses serialization equality and lacks runtime validation
**Priority: Medium-Low | File:** `migrateVsCodeState.ts`

`deepEqual` uses `JSON.stringify` for objects/arrays; comparison depends on property serialization order and JSON serializability. Legacy values are generically typed/cast rather than validated against destination schemas.

**Direction:** validate each legacy key and use structural JSON equality; test malformed values and reordered keys.

### A-09 — Workspace/project identity embeds multi-root assumptions
**Priority: Medium | Files:** `hostDirectories.ts`, `agentManager.ts`, `PixelAgentsViewProvider.ts`

Default cwd is first workspace folder or home. Multi-root labels choose the longest containing workspace path; external sessions may be resolved by hashed project-directory names. Scans start with the first project root and then add remaining roots.

**Impact:** nested roots, symlinks, case normalization and provider hash conventions need cross-platform evidence.

**Direction:** define canonical workspace/project identity and overlap/symlink/case policy; test Windows/macOS/Linux.

### A-10 — Uninstall cleanup is best-effort asynchronous module work
**Priority: Low-Medium | File:** `uninstall.ts`

A top-level promise chain uninstalls provider hooks, then usage bridges, and calls `resetHooksConfig()` in `finally`. Errors are log-only; copied hook script remains because standalone CLI shares it. Process completion semantics were not verified.

**Direction:** verify uninstall-hook process semantics; provide bounded completion and observable exit status.

## Confirmed constraints

1. `VscodeTerminalAdapter` exposes active/all terminals; it does not create or launch terminals.
2. VS Code launches terminals directly; standalone uses server PTY/session infrastructure.
3. VS Code agent state is namespaced in shared machine persistence; layout/config/profile data is shared across surfaces.
4. Hook settings/consent are provider-aware in parts of UI, but launch/scanning remains Claude-specific.
5. Extension bundle uses Node platform with `vscode` external; uninstall is a separate plain-Node bundle.
6. Webview readiness buffers up to 1,000 broadcasts, dropping oldest on overflow.

## Test/evidence map

- `migrateVsCodeState.test.ts`: settings migration, intentional hooks preference discard, agents/seats, no-op/idempotence, layout migration, preserve existing layout, simulated destination write failure.
- `fileStateAdapter.test.ts`: namespace settings/state persistence and VS Code/standalone isolation.
- No dedicated tests found in the inspected test inventory for activation/deactivation, provider lifecycle, repeated webview resolution, terminal launch compensation, restore timer cancellation, multi-root VS Code host behavior, hook orchestration in VS Code, or uninstall completion.
- Tests were inspected statically, not executed.

## Relationship to server findings

| Server finding | Adapter relationship |
|---|---|
| F-01 module-global runtime state | A-01 registers VS Code callbacks globally without unregistering on dispose. |
| F-02 first-root /clear fallback | Adapter scans multiple roots but does not repair server's first-root capture. |
| F-03 stale session route | A-03 explicitly unregisters on some close paths; runtime removal does not enforce it universally. |
| F-04 provider identity dropped | A-04 confirms launch is Claude-specific; provider-aware hook UI is not generic provider runtime routing. |
| F-05 server close not awaited | Provider dispose invokes `pixelAgentsServer.stop()` without awaiting. |
| F-06 token file mode | No direct adapter handling; server-owned. |
| F-07 JSONL descriptor leak | Adapter calls watcher APIs; defect remains server-owned. |
| F-08 message validation | Webview controller casts incoming fields; validation remains uneven across shared message handling. |

## Exit status

- [x] Recursive tree retrieved without truncation.
- [x] All 8 adapter files fetched and statically reviewed.
- [x] Relevant migration/persistence tests inspected.
- [x] Build/package entry and cross-module runtime seams inspected.
- [ ] Test execution, typecheck, lint and build.
- [ ] VS Code host integration and platform matrix.
- [ ] Repository-wide cross-module audit.

**Conclusion:** static audit of `adapters/` is complete. Dynamic verification and repository-wide integration audit remain open. Next planned module: `webview-ui/`.

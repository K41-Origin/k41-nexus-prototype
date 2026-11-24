# Server File Reference

**Status:** STATIC INVENTORY RECONCILED — 135 tracked files under `server/`  
**Baseline:** `f1af3e0c36573e4317143969cf43dde642877ba6`  
**Counts:** 74 files in `server/src/`; 57 files in `server/__tests__/` (including 1 fixture and 1 setup); 4 root support/config files.

This reference covers every tracked blob in the server subtree at the baseline. All 74 runtime source files were fetched and reviewed; all 57 test/support files were fetched and their suites/cases inventoried; all 4 root support/config files were reviewed. Responsibilities are summarized from source, imports, comments and inspected call paths. Test descriptions identify their apparent scope; they do **not** assert that tests were executed or passed. See [Deep Audit Findings](DEEP-AUDIT.md) for architectural findings and remaining verification.

## Runtime source — `server/src/` (74 files)

### Agent state and lifecycle

| File | Responsibility and relationship |
|---|---|
| `agentRuntime.ts` | Shared lifecycle coordinator used by VS Code and standalone. Wires hook handler, scanners, timers, dismissal tracker, subagent watch, team/background callbacks, restoration and cleanup. |
| `agentState.ts` | Canonical `createAgentState()` initializer for runtime defaults across launch/adoption/restore paths. |
| `agentStateStore.ts` | In-memory agent Map owner; typed events, broadcast fan-out, StateAdapter persistence and optional diagnostic broadcast log. |
| `types.ts` | Server runtime `AgentState` and related mutable state shapes; richer than persisted/core agent contracts. |
| `agentPersistence.ts` | Converts runtime agents to/from core `PersistedAgent`; filters derived spawn children and supports legacy fields. |
| `agentMessages.ts` | Builds the canonical `agentCreated` wire message for both host surfaces. |
| `agentActivityResend.ts` | Replays current team/tool/status/context activity to a newly connected client in required order. |
| `agentDiagnostics.ts` | Builds filesystem/transcript diagnostics payloads for Debug View. |
| `backgroundSpawns.ts` | Tracks live async spawns, classifies teammate/subagent, persists spawn identity, parses completion/pause notices and judges paused fate. |
| `subagentWatch.ts` | Shadow AgentStateStore watches unnamed spawn transcripts and translates normal tool activity into existing subagent protocol messages. |
| `teamUtils.ts` | Pure lead/teammate relationship helpers, inline teammate checks and promoted-spawn detection. |
| `sessionRouter.ts` | Session-ID mapping, pending external sessions and short-lived buffer for hook events arriving before agent registration. |
| `hookEventHandler.ts` | Consumes provider-normalized AgentEvent, routes lifecycle/tool/permission/team events, invokes runtime callbacks and emits store broadcasts. |
| `transcriptParser.ts` | Parses JSONL records for tools, progress, usage, teams and spawn state; owns process-level active HookProvider and injected callbacks. |
| `timerManager.ts` | Waiting/permission heuristic timers and activity clearing; hook-delivered state suppresses selected fallbacks. |
| `dismissalTracker.ts` | Tracks temporary user dismissals, permanent /clear dismissals, seeded mtimes and pending clear files. |
| `pathKey.ts` | Canonical filesystem path keys and PathSet; case-folds on Windows to unify hook/scanner path identity. |
| `fileWatcher.ts` | JSONL polling, project/global/external scans, session adoption/reassignment, teammate/background discovery and watcher/timer orchestration. Large mixed-responsibility module. |
| `contextUsage.ts` | Extracts context tokens from transcripts, maps model windows, seeds from transcript tail and applies CLI-reported context. |

### HTTP, client protocol and host runtime

| File | Responsibility and relationship |
|---|---|
| `server.ts` | `PixelAgentsServer` lifecycle; embedded/standalone capability reuse, Fastify creation, legacy + multi-server discovery records and cleanup. |
| `httpServer.ts` | Fastify composition; health, hook, usage, office WebSocket and terminal routes; socket/store event bridge and logging redaction. |
| `clientMessageHandler.ts` | Central client command dispatcher and webviewReady state handshake; composes agents, settings, assets, profiles, directories, consent, conversations, usage and terminal launch. |
| `serverConfig.ts` | Server discovery/standalone access types, validation and compatibility predicates. |
| `serverToken.ts` | Reads/creates/rotates persisted standalone privilege token. |
| `wsAuth.ts` | Shared token, Origin, Host allowlist and standalone privilege predicates; token query redaction. |
| `cli.ts` | Standalone CLI parsing, startup composition, asset/provider/runtime/PTY initialization, consent/hook setup, scan start, URL output and signal shutdown. |
| `constants.ts` | Server timing, limits, persistence filenames, registry version, WebSocket close codes and PTY defaults; re-exports core constants. |

### Provider registry and consent

| File | Responsibility and relationship |
|---|---|
| `providers/index.ts` | Bundled provider registry; currently registers only Claude Code. |
| `providers/hook/consentGate.ts` | Provider-agnostic first-run consent ask policy and choice-to-action mapping. |
| `providers/hook/consentExecutor.ts` | Serialized per-process consent action executor; delegates host effects and persists answer state. |

### Claude Code provider

| File | Responsibility and relationship |
|---|---|
| `providers/hook/claude/claude.ts` | Claude HookProvider implementation: tool formatting, session paths, launch command, raw hook normalization, context windows and provider composition. |
| `providers/hook/claude/constants.ts` | Claude-specific hook names/events, context windows, subagent tools and settings mutation constants. |
| `providers/hook/claude/bundledScripts.ts` | Canonical installed script paths and copy/chmod of bundled scripts into user home. |
| `providers/hook/claude/claudeHookEntries.ts` | Constructs owned Claude hook entries and determines command/entry identity without touching disk. |
| `providers/hook/claude/commandIdentity.ts` | Parses first command token and checks whether it runs an owned absolute script path. |
| `providers/hook/claude/claudeHookInstaller.ts` | Installs/uninstalls Pixel Agents hook entries while preserving foreign entries and refusing malformed settings. |
| `providers/hook/claude/claudeSettingsFile.ts` | Shared guarded settings.json read-modify-write, one-time backup, file-mode preservation, atomic rename and concurrent-write retry. |
| `providers/hook/claude/consentCopy.ts` | User-facing consent disclosure text derived from installed event list. |
| `providers/hook/claude/sessionEnv.ts` | Identifies Claude session-specific environment markers that must not be inherited by nested launched sessions. |
| `providers/hook/claude/conversationTitle.ts` | Reads custom/AI/last-prompt title records from bounded transcript head/tail windows. |
| `providers/hook/claude/claudeTeamProvider.ts` | Claude-specific team metadata, sidecar and transcript discovery, spawn result and completion-notice parsing, team membership. |
| `providers/hook/claude/claudeUsageBridge.ts` | Normalizes Claude status-line rate-limit and context reports; composes status-line bridge installer. |
| `providers/hook/claude/statusLineBridge.ts` | Encodes/decodes the user's wrapped status-line command and recognizes owned status-line shapes. |
| `providers/hook/claude/statusLineInstaller.ts` | Installs/uninstalls/refreshes status-line wrapper through guarded settings mutation. |
| `providers/hook/claude/hooks/claude-hook.ts` | Bundled executable hook producer: reads stdin, discovers servers and POSTs hook payload to all live servers. |
| `providers/hook/claude/hooks/claude-statusline.ts` | Bundled status-line wrapper: runs user's command and sends minimized usage/context payload. |
| `providers/hook/claude/hooks/serverRegistry.ts` | Shared bundled-script discovery/validation of live server records and bounded local HTTP POST helper. |

### Persistence, directories, assets and user-facing data

| File | Responsibility and relationship |
|---|---|
| `configPersistence.ts` | Shared machine-wide config schema, defensive parsing, hooks consent/preferences, atomic config write. |
| `fileStateAdapter.ts` | Core StateAdapter implementation; namespace-specific settings and persisted agents/seats/card order. |
| `jsonFileWatcher.ts` | Atomic JSON writer and cross-process fs.watch + polling watcher with own-write suppression marks. |
| `layoutPersistence.ts` | Reads/writes layout, applies bundled revision reset and watches external changes. |
| `characterProfilesStore.ts` | Cached validated characters/wardrobe JSON under home directory. |
| `characterProfilesPersistence.ts` | Saves/watches profiles and wardrobe; handles client messages and palette/reference remapping. |
| `characterRemap.ts` | Remaps agent looks, saved seats, Directory defaults, recent roster and conversation looks after profile changes. |
| `cardOrder.ts` | Parses, filters and persists agent card order through StateAdapter. |
| `paletteAssigner.ts` | Server-authoritative palette/repeat selection using core chooser, profiles, external sprite count and Directory reservations. |
| `recentCharacters.ts` | Machine-wide most-recently-used character roster. |
| `directories.ts` | Merges host/user directories, validates paths, persists CRUD and dispatches Directory messages. |
| `hostDirectory.ts` | Standalone host's cwd contribution and path display-name helper. |
| `directorySuggestions.ts` | Recovers recently used working directories from provider transcript roots. |
| `conversations.ts` | Lists/searches/resumes provider transcripts and persists per-session character/seat looks. |
| `usageLimits.ts` | Persists provider usage windows, emits privileged usage messages and toggles UsageBridge. |
| `assetLoader.ts` | Loads bundled/external sprite, furniture, tile, pet and layout assets; inspect source for exact loader/merge behavior. |
| `assetReload.ts` | Shared load/merge orchestration and construction of standalone AssetCache. |

### Standalone terminal subsystem

| File | Responsibility and relationship |
|---|---|
| `terminal/terminalSessions.ts` | Terminal session/session-manager contracts, replay shape and shared event emitter. |
| `terminal/terminalEnv.ts` | Builds sanitized inherited environment, TERM/locale defaults and session marker unsets. |
| `terminal/ptyModule.ts` | Dynamically resolves optional native PTY module and probes spawn viability. |
| `terminal/ptySessionManager.ts` | In-process PTY ownership, headless xterm mirror, serialized replay, resize/write/kill and shutdown. |
| `terminal/terminalRoutes.ts` | Privileged terminal WebSocket attach, replay/live output handoff and client input/resize frames. |
| `terminal/standaloneAgentLauncher.ts` | Launches/resumes Claude in PTY, registers agent/transcript, persists, restores surviving PTY-host agents. |
| `terminal/ptyHostProtocol.ts` | Versioned newline-delimited JSON protocol, message types, decoder and Unix socket path helpers. |
| `terminal/ptyHost.ts` | Long-lived development PTY host process; owns sessions and serves local socket requests. |
| `terminal/ptyHostMain.ts` | PTY host executable entry point and signal/idle shutdown. |
| `terminal/remotePtySessions.ts` | Server-side TerminalSessions proxy to detached PTY host; startup, request correlation, reconnect state and survivor tracking. |

## Tests and test support — `server/__tests__/` (57 files)

These files are part of the tracked server inventory. Their names indicate focused test areas; assertions and current pass status require running/reviewing the test suite.

| File | Apparent test scope |
|---|---|
| `agentActivityResend.test.ts` | Replaying agent activity on client connection |
| `agentDiagnostics.test.ts` | Agent filesystem/runtime diagnostics |
| `agentRuntime.restoreDirectoryName.test.ts` | Restoring directory labels |
| `agentRuntime.restorePalette.test.ts` | Restoring agent appearance |
| `agentRuntime.test.ts` | Shared runtime lifecycle |
| `agentStateStore.test.ts` | Store events, broadcast and persistence |
| `assetReload.test.ts` | Asset reload/cache composition |
| `backgroundAgents.test.ts` | Background agent/teammate lifecycle |
| `backgroundSpawns.test.ts` | Spawn classification, persistence and completion |
| `characterProfiles.test.ts` | Character profile behavior |
| `characterProfilesPersistence.test.ts` | Profile/wardrobe persistence and watcher flow |
| `characterRemap.test.ts` | Palette/reference remapping |
| `claude-hook.test.ts` | Bundled hook script delivery |
| `claude-statusline.test.ts` | Bundled status-line wrapper/reporting |
| `claude.test.ts` | Claude provider normalization and helpers |
| `claudeHookInstaller.test.ts` | Hook install/uninstall and preservation rules |
| `claudeSettingsOwnership.test.ts` | Claude settings ownership/backup/concurrency rules |
| `claudeTeamProvider.test.ts` | Claude team and sidecar parsing |
| `cli.test.ts` | CLI arguments and startup-related behavior |
| `clientMessageHandler.test.ts` | WebSocket client command dispatch and handshake |
| `configPersistence.test.ts` | Shared config parse/write/consent behavior |
| `consentCopy.test.ts` | Consent disclosure content |
| `consentFlow.test.ts` | End-to-end consent decision effects |
| `consentGate.test.ts` | Consent policy decision table |
| `contextUsage.test.ts` | Transcript/bridge context calculations |
| `conversations.test.ts` | Conversation list/search/resume/look persistence |
| `coreGuards.test.ts` | Guard against server imports in core |
| `directories.test.ts` | Directory validation/union/CRUD |
| `directoryCharacters.test.ts` | Directory default character handling |
| `directorySuggestions.test.ts` | Transcript-derived directory suggestions |
| `fileStateAdapter.test.ts` | Adapter namespace persistence |
| `fileWatcherDismissal.test.ts` | Dismissal and file watcher adoption behavior |
| `fixtures/taskNotifications.ts` | Shared task notification test fixture data |
| `hookEventHandler.test.ts` | Hook routing and normalized event handling |
| `httpServerLog.test.ts` | HTTP request log token redaction |
| `httpServerWs.test.ts` | WebSocket connection/message behavior |
| `initialReadOffset.test.ts` | Transcript initial read offset behavior |
| `isolateHome.setup.ts` | Test setup isolating HOME and user files |
| `jsonFileWatcher.test.ts` | JSON watcher and own-write suppression |
| `migrateVsCodeState.test.ts` | VS Code state migration coverage from server test harness |
| `mockClaudeRunner.test.ts` | Mock Claude process/runner helpers |
| `paletteAssigner.test.ts` | Character palette assignment |
| `ptyHost.test.ts` | PTY host socket protocol/lifecycle |
| `ptySessionManager.test.ts` | In-process PTY session lifecycle/replay |
| `recentCharacters.test.ts` | Recent character roster |
| `seatedFrame.test.ts` | Seated frame helper coverage |
| `server.test.ts` | PixelAgentsServer start/reuse/registry/stop |
| `sessionRouter.test.ts` | Session registration, pending sessions and buffering |
| `standaloneAgentLauncher.test.ts` | Standalone launch/restore/reattach |
| `statusLineInstaller.test.ts` | Status-line install/uninstall behavior |
| `teamUtils.test.ts` | Lead/teammate relationship helpers |
| `terminalFrames.test.ts` | Core terminal frame integration |
| `terminalRoutes.test.ts` | Terminal WebSocket auth and frame forwarding |
| `transcriptParser.test.ts` | JSONL transcript record parsing |
| `usageLimits.test.ts` | Usage bridge persistence/privileged reporting |
| `wardrobeItems.test.ts` | Wardrobe item validation |
| `wsAuth.test.ts` | Token, Origin and Host privilege predicates |

## Root support/config files (4 files)

| File | Responsibility |
|---|---|
| `server/package.json` | Workspace package metadata, server test scripts and direct runtime/test dependencies. |
| `server/tsconfig.test.json` | TypeScript test compilation settings and included source/test paths. |
| `server/vitest.config.ts` | Vitest discovery, timeout, setup, default reporter and optional Allure output. |
| `server/manual-hook-events.http` | Manual HTTP request examples for exercising provider hook endpoint. |

## Inventory reconciliation

The recursive Git tree at the baseline returned `truncated: false`. The 135 blobs are reconciled as 74 runtime source files + 57 test/support files under `__tests__` + 4 root support/config files. Directory entries are not counted as files. No tracked blob under `server/` is intentionally omitted from the tables above.

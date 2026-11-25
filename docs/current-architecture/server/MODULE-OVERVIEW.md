# Server Module Overview

**Status:** STATIC SOURCE AUDIT DOCUMENTED — runtime verification pending

## Responsibility

`server/` is the Node.js execution and integration layer shared by two hosts:
- **Embedded:** VS Code adapter owns editor terminals and contributes workspace directories; server supplies runtime, persistence, hook ingestion and WebSocket transport.
- **Standalone:** CLI owns Fastify, static SPA, optional PTY terminals, launch/resume, and process shutdown.

The server observes external coding-agent sessions. It does not submit prompts to an LLM, choose models, run inference, or implement a general agent planner/orchestrator.

## Internal subsystems

| Subsystem | Main files | Responsibility |
|---|---|---|
| Agent state/runtime | `agentRuntime.ts`, `agentStateStore.ts`, `agentState.ts`, `types.ts` | Agent lifecycle, state ownership, restore, timers, scanners and callbacks |
| Transcript/session observation | `fileWatcher.ts`, `transcriptParser.ts`, `hookEventHandler.ts`, `sessionRouter.ts`, `contextUsage.ts` | JSONL polling, hook normalization dispatch, session mapping, activity/context derivation |
| Provider integration | `providers/` | Hook provider registry, Claude-specific payload normalization, hook/status-line install and consent |
| Team/background tracking | `backgroundSpawns.ts`, `subagentWatch.ts`, `teamUtils.ts`, `claudeTeamProvider.ts` | Sidecar discovery, team identity, spawn completion and shadow transcript observation |
| Client protocol handling | `httpServer.ts`, `clientMessageHandler.ts`, `agentMessages.ts`, `agentActivityResend.ts` | HTTP/WebSocket boundary, client commands, state snapshots and broadcasts |
| Persistence | `fileStateAdapter.ts`, `configPersistence.ts`, `layoutPersistence.ts`, `characterProfilesPersistence.ts`, `conversations.ts`, `usageLimits.ts` | Local JSON persistence and cross-process file watching |
| Asset handling | `assetLoader.ts`, `assetReload.ts` | Load bundled/external sprites and build cache |
| Directories/conversations | `directories.ts`, `directorySuggestions.ts`, `conversations.ts`, `recentCharacters.ts` | Launch locations, suggestions, recent sessions and resume discovery |
| Terminal/PTY | `terminal/` | Standalone process launch, PTY lifecycle, screen replay, socket protocol and terminal WebSocket |
| Entry/discovery | `cli.ts`, `server.ts`, `serverConfig.ts`, `serverToken.ts` | Standalone boot, Fastify ownership, multi-server registry and token |
| Diagnostics/utility | `agentDiagnostics.ts`, `pathKey.ts`, `dismissalTracker.ts`, `timerManager.ts`, `constants.ts` | Debug payloads, path identity, suppression state, heuristic timers and shared constants |

## Major data paths

1. Provider hook script reads stdin, discovers live server records, then POSTs to each server's authenticated hook endpoint.
2. `httpServer.ts` validates the bearer token and calls the host callback; `PixelAgentsServer` forwards the event to the runtime.
3. `HookEventHandler` normalizes/routs session events; `fileWatcher.ts` separately polls transcript JSONL for detailed tool/content data.
4. `AgentStateStore` emits add/remove/broadcast events. WebSocket connections translate these into agent lifecycle messages and forward broadcasts.
5. A new client sends `webviewReady`; the server returns state snapshots, cached assets, settings, directories, persisted layout and provider status.
6. Standalone client commands may launch/resume an agent through a PTY; terminal output is mirrored and streamed through a dedicated WebSocket route.

## External boundaries

- Core contracts/types: `StateAdapter`, `PersistedAgent`, `HookProvider`, `AgentEvent`, `TeamProvider`, `UsageBridge`, protocol messages and terminal frames.
- Node.js: filesystem, home directory, process signals/PIDs, child processes, Unix sockets, timers and environment.
- Network: Fastify HTTP and WebSocket; local hook/usage POSTs.
- Provider: Claude Code CLI, `~/.claude/projects`, `~/.claude/settings.json`, team sidecars/config and status-line interface.
- Optional native PTY: `@lydell/node-pty` then `node-pty`.

## Ownership and coupling observations

- `AgentRuntime` centralizes shared session observation, but platform-specific lifecycle remains in VS Code adapter and standalone launcher.
- `fileWatcher.ts` is a large multi-responsibility module: scanner orchestration, JSONL I/O, adoption/reassignment, teammate/background discovery, and module-level injected callbacks.
- `clientMessageHandler.ts` is a large switch-based application command dispatcher and composes persistence, consent, assets, directories, conversations and terminal launch.
- `configPersistence.ts` is a shared machine-wide config authority; `FileStateAdapter` scopes agent/seat/card state by `vscode` or `standalone`.
- Provider abstraction currently has one registered implementation: Claude Code. Other provider kinds are not yet implemented.
- `server/` contains executable product behavior, not merely transport glue.

## Known audit limitations

Static inspection does not establish race-free concurrent writes, runtime lifecycle correctness, security under real proxy deployments, PTY reliability, or cross-platform compatibility. See [runtime lifecycle](RUNTIME-LIFECYCLE.md), [HTTP/WebSocket](HTTP-WEBSOCKET.md), and [dependencies](DEPENDENCIES.md).

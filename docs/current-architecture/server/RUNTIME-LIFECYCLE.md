# Server Runtime Lifecycle

**Status:** STATIC SOURCE AUDIT DOCUMENTED — execution not verified

## Embedded lifecycle (VS Code)

1. VS Code adapter constructs the shared `AgentStateStore`, `FileStateAdapter(namespace: vscode)`, provider/runtime and platform callbacks.
2. `AgentRuntime` installs its shared callback wiring: dismissal tracker, hook provider/team provider, subagent shadow store, hook event handler, teammate/background callbacks and lifecycle handlers.
3. `PixelAgentsServer.start({ embedded: true, ... })` searches the multi-server registry for a compatible embedded instance. If found, it reuses its `ServerConfig`; otherwise it creates Fastify on the requested/default ephemeral port with a per-process UUID token.
4. An owned server writes both legacy `~/.pixel-agents/server.json` and its own `servers/<pid>-<port>.json` registry entry. Embedded mode does not serve SPA and editor owns terminal lifecycle.
5. Adapter starts relevant project/external scans and file watchers. The exact workspace/terminal wiring is adapter-owned, not server-owned.

## Standalone lifecycle

1. `cli.ts` parses `--port`, `--host`, `--allowed-host`, `--rotate-token`, `--no-terminal`, and development-only `--pty-host` options.
2. Resolve package/dist roots; load bundled and external assets; build the in-memory `AssetCache`; construct `FileStateAdapter(namespace: standalone)`, `AgentStateStore`, `AgentRuntime`, and Claude provider.
3. Create terminal manager: disabled manager for `--no-terminal`, in-process `PtySessionManager` by default, or `RemotePtySessions` connected to a detached PTY host in development mode.
4. Start `PixelAgentsServer` with SPA directory, cache, runtime and terminal manager. Standalone server uses a persisted token from `standalone-token`; token rotation is refused while another standalone server is live.
5. Restore external persisted agents; in PTY-host mode reattach surviving terminals to persisted records before accepting clients. The runtime watches transcripts and starts stale/paused-spawn checks.
6. Hook consent is handled in the UI handshake, not by an interactive CLI prompt. Existing pre-consent installations may be migrated silently where the CLI's code allows it. When consent and hooks preference permit, copy the bundled hook script and install Claude settings entries.
7. Scan the current working directory's provider session directory; optionally scan global sessions according to settings.
8. Print the token-bearing local URL. Signal handlers call graceful shutdown.

## Agent lifecycle

- Creation paths include standalone PTY launch, external session adoption by scanner/hook, teammate discovery, background spawn promotion and restoration.
- `createAgentState()` initializes common runtime scaffolding.
- `AgentStateStore.set()` emits `agentAdded`; HTTP/WebSocket translates this to `agentCreated`.
- Runtime registers session-to-agent mapping and watches JSONL. The hook path gives low-latency lifecycle signals; JSONL polling supplies tool details and fallback heuristics.
- Close removes teammate/subagent children, stops watchers/timers, dismisses the transcript as needed, removes store state and persists.
- External agents can be restored from `StateAdapter`; standalone terminal agents can be restored only when their PTY survived in the separate PTY host.

## Hook and transcript lifecycle

- Hook scripts discover all live registry entries and POST events to `/api/hooks/:providerId`; if no registry entry exists they fall back to legacy `server.json`.
- The HTTP route authenticates via Bearer token and calls `PixelAgentsServer.onHookEvent`.
- Runtime's `HookEventHandler` maps session IDs to agents, buffers early events, may hold pending external sessions, and dispatches normalized `AgentEvent` kinds.
- JSONL poller reads appended bytes in bounded chunks, buffers partial lines and calls provider-aware transcript parsing. Hook delivery suppresses duplicate heuristic timers but does not replace transcript parsing.
- SessionEnd(clear/resume) gets a grace period for a following SessionStart; final session ends trigger cleanup.

## Shutdown and recovery

- `PixelAgentsServer.stop()` closes Fastify and removes discovery files only when this process owns them.
- `AgentRuntime.stop()` stops hook handler, shadow watches, scanners, timers and per-agent watchers but leaves store contents for restore.
- `AgentRuntime.dispose()` removes agents and clears persisted state; standalone normal PTY shutdown disposes runtime because child processes are terminated.
- Standalone PTY-host shutdown disconnects from host and calls runtime stop, preserving persisted agent records so next server can reattach. Stopping the PTY host itself terminates all terminals.
- PTY sessions use SIGHUP then SIGKILL escalation and bounded shutdown wait. Terminal sockets detach without killing the underlying process.

## Recovery / edge cases visible in source

- Stale registry entries are pruned by PID liveness; malformed registry files are removed.
- Server registry key uses PID+port to avoid same-process collisions.
- Hook delivery fans out to multiple compatible servers.
- PTY host has protocol version handshake, startup retry/timeout and idle exit.
- Agent transcript paths are normalized for Windows path identity; standalone reattachment can relocate a transcript path by session filename.
- JSONL read errors and watcher failures are generally tolerated/logged rather than fatal.

## Verification still required

No lifecycle was executed during this static audit. Run server unit tests, root build, CLI smoke tests, hook integration tests, embedded/standalone dual-server tests, PTY attach/replay/exit tests and shutdown/restart recovery on supported OSes before treating these paths as runtime-verified.

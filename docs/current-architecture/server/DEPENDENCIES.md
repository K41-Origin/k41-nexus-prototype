# Server Dependencies

**Status:** STATIC SOURCE AUDIT DOCUMENTED

## Internal dependency rules

- Server source imports shared contracts and runtime helpers from `core/src/**`.
- Core does not import server (consistent with the documented layering).
- Server is not a leaf: it imports Node built-ins and runtime packages and is consumed by VS Code adapter and standalone CLI.
- Some server modules are deliberately shared across both hosts; host-specific effects are injected through callbacks/interfaces.

## Runtime package dependencies

From `server/package.json`:
- Fastify 5 and plugins: `fastify`, `@fastify/cors`, `@fastify/static`, `@fastify/websocket`.
- Terminal rendering/replay: `@xterm/headless`, `@xterm/addon-serialize`.
- WebSocket types/runtime package: `ws`.
- Tests: `vitest`, `allure-vitest`, `@types/ws`.

Native PTY:
- Runtime resolver tries `@lydell/node-pty`, then `node-pty`.
- PTY availability is established by spawning a probe on non-Windows platforms, not import success alone. On Windows the spawn probe is intentionally skipped.
- The PTY dependency is optional at runtime; no available module disables terminal launch while leaving the office usable.
- Development-only `--pty-host` adds child process, Unix socket and a versioned newline-JSON protocol.

## Node/platform facilities

Server source uses filesystem/path/os/crypto/events/util, HTTP/net/child_process, process environment/signals/PID checks, timers, Buffer and Unix-domain sockets. The feature set is therefore Node/runtime and OS dependent; browser code must not import server-only entry points.

## External systems

- Claude Code CLI executable (`claude`) for standalone agent launches.
- Claude hook interface and status-line command interface.
- Claude filesystem layout under `~/.claude`: settings, project transcripts, sidecars, team config.
- Local HTTP loopback delivery from installed hook/status-line scripts to server instances.
- No outbound LLM provider SDK/API client appears in the audited server package.

## Architectural observations

- `server/package.json` is intentionally small; most imports are workspace/core source and Node built-ins.
- `ws` appears in dependencies alongside Fastify WebSocket plugin; actual runtime use should be checked before removing.
- PTY module is dynamically required to avoid bundling native code.
- `server/src/fileWatcher.ts` imports the VS Code type namespace for an adapter-facing terminal type while runtime implementation remains Node-based; inspect emitted type-only behavior before changing boundaries.
- Some shared modules use module-level singleton registration/callbacks (`transcriptParser`, `fileWatcher`, `paletteAssigner`, `usageLimits`). This is a process-level ownership constraint, especially for tests or multiple runtimes in one process.

## Verification pending

No dependency graph tool, bundle analysis, package install, build, test or platform matrix was run in this audit. Validate actual package reachability and optional dependency packaging before making dependency-removal or packaging claims.

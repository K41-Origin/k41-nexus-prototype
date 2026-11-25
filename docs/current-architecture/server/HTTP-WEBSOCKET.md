# HTTP and WebSocket Architecture

**Status:** STATIC SOURCE AUDIT DOCUMENTED — live network/security tests pending

## HTTP routes

| Method | Route | Purpose | Authentication / limits |
|---|---|---|---|
| POST | `/api/hooks/:providerId` | Receive provider hook event | Bearer token; providerId pattern; Fastify body limit 65,536 bytes |
| POST | `/api/usage/:providerId` (prefix from core constant) | Receive usage/context bridge report | Bearer token; providerId pattern |
| GET | `/api/health` | Health, uptime and PID | No auth |
| GET | `/ws` (upgrade) | Office state/control WebSocket | Embedded Bearer; standalone same-origin connection gate plus separate token+Host privilege verdict |
| GET | `/terminal/:agentId` (core terminal prefix) | Attach to existing standalone PTY | Token + allowed Host + same-origin; only attaches, never spawns |

Standalone may also serve static SPA files and uses an HTML5 history fallback. Embedded mode disables static serving and Fastify request logging.

## WebSocket behavior

- `httpServer.ts` subscribes each connected socket to `AgentStateStore` events:
  - `agentAdded` → `agentCreated`
  - `agentRemoved` → `agentClosed`
  - `broadcast` → message, filtered for privileged-only types.
- Incoming JSON is parsed and dispatched to `handleClientMessage()`. Malformed JSON is ignored.
- `webviewReady` handshake returns initial state (agent creation/activity, settings, assets, directories, layout, profiles, card order, recent characters, provider hook/usage status and terminal availability as applicable).
- Client command handling covers launch/close/resume, layout and seat saves, settings, consent, usage bridge, directories, profiles, assets, card order, area mappings and diagnostics.
- Standalone unprivileged clients may connect read-only; `handleClientMessage` refuses every message except `webviewReady`. Privileged-only usage broadcasts are also filtered.

## Authentication and trust boundaries

- Hook and usage HTTP endpoints use `Authorization: Bearer <token>` checked with constant-time string comparison.
- Embedded `/ws` requires the bearer token supplied by the VS Code host.
- Standalone `/ws` connection gate checks same-origin using Origin host == request Host; missing Origin is accepted for non-browser local clients. This is a connection gate, not proof of privilege.
- Standalone privilege requires token query parameter AND Host in allowlist (loopback, specific bind hostname, explicit `--allowed-host`). Tokenless accepted sockets remain read-only.
- Terminal route uses the same standalone privilege predicate plus same-origin check. It is the most sensitive route because input is delivered to a shell running as the user.
- Token query values are redacted from Fastify request logs.
- Binding terminal-enabled standalone server to a non-loopback host logs a warning: HTTP is unencrypted and token/terminal access is powerful.

## Terminal WebSocket data plane

The terminal route subscribes to PTY output/exit, obtains a serialized screen replay and sends it first, then flushes buffered live frames whose sequence exceeds replay's sequence. This snapshot/live handoff is intended to avoid missing or duplicating output across attach/reconnect. Client frames are parsed with core terminal frame helpers; malformed frames are ignored. Socket close detaches listeners but leaves PTY alive.

## Risks / verification

- Same-origin and token/Host checks need tests against real browsers, DNS rebinding, reverse proxies and Host normalization edge cases.
- `@fastify/cors` origin reflection does not replace WebSocket authentication.
- The route table and trust model were statically read; no live network attack or proxy test was run.
- Client-message validation is mixed: some handlers validate carefully, while others cast values from untrusted JSON. The top-level privilege gate is not equivalent to per-message schema validation.
- Confirm route prefixes against `core/src/constants.ts` and current tests before changing endpoints.

# Adapters Module Overview

## 1. Actual role

`adapters/vscode/` is the VS Code host composition layer. It:
- Implements the VS Code extension activation/deactivation lifecycle.
- Constructs the shared server-side state adapter with namespace `vscode`.
- Migrates legacy VS Code Memento state into shared machine-level persistence.
- Owns the WebviewView and translates webview messages into server/domain operations.
- Supplies host-specific workspace directories and terminal access.
- Launches, restores, tracks, and removes Claude Code terminal sessions.
- Starts an embedded `PixelAgentsServer`, connects hook events to `AgentRuntime`, and coordinates hook consent/installation.
- Loads and sends webview assets/layout and bridges VS Code dialogs, terminals, filesystem and workspace APIs.
- Cleans provider hook configuration on extension uninstall.

It is tightly coupled to the current Pixel Agents product and Claude provider. It is not presently a reusable generic host-adapter framework.

## 2. Files

| File | Responsibility |
|---|---|
| `extension.ts` | VS Code activate/deactivate; registers view and commands; creates `FileStateAdapter(namespace: 'vscode')`; invokes migration. |
| `PixelAgentsViewProvider.ts` | Main VS Code composition root and webview controller; creates store/runtime/server, message handling, host event handling, hooks, assets, directories, disposal. |
| `agentManager.ts` | VS Code terminal launch/restore/remove; JSONL watcher/timer orchestration; persistence projection and webview snapshot messages. |
| `constants.ts` | VS Code setting/command/view identifiers and re-exports shared persistence constants from server. |
| `hostDirectories.ts` | Converts VS Code workspace folders to shared host directory entries. |
| `migrateVsCodeState.ts` | Moves legacy global/workspace Memento values into shared file-backed persistence. |
| `vscodeTerminalAdapter.ts` | Implements core `ITerminalAdapter` using VS Code terminal APIs. |
| `uninstall.ts` | Node-only post-uninstall cleanup for provider hooks and usage bridges. |

## 3. Composition and dependencies

Build entry: `esbuild.js` bundles `adapters/vscode/extension.ts` as `dist/extension.js`, with `vscode` external. The uninstall hook is a separate Node bundle at `dist/uninstall.js`. Root `package.json` declares extension main, activation event, workspace extension kind and uninstall script.

Dependency direction observed:
- Adapter → core contracts/types: `StateAdapter`, `PersistedAgent`, `ITerminalAdapter`, palette/provider contracts.
- Adapter → server runtime/services: `AgentRuntime`, `AgentStateStore`, `PixelAgentsServer`, file watcher, persistence, directories, providers, assets, consent and usage modules.
- Adapter → VS Code API: workspace folders, terminals, webviews, commands, dialogs, extension context and notifications.
- Adapter → Node APIs: filesystem, OS, paths, process environment, buffers and crypto globals.

The adapter is therefore not a thin interface-only shell: it contains substantial orchestration and product policy.

## 4. Runtime lifecycle

1. VS Code invokes `activate(context)`.
2. Activation constructs `FileStateAdapter({ namespace: 'vscode' })`, performs synchronous migration, constructs `PixelAgentsViewProvider`, registers the webview provider and commands.
3. Provider constructor attaches store listeners, character-profile watcher, workspace-folder listener, global file-watcher terminal/directory resolver registrations, constructs `AgentRuntime`, then starts the embedded server asynchronously.
4. When the webview view resolves, HTML is loaded and a message listener is attached. The UI's `webviewReady` handshake flushes buffered broadcasts, sends settings/provider state, restores agents, registers restored sessions, optionally auto-spawns, starts scans/stale checks, loads assets/layout, and sends snapshots.
5. User webview messages launch/focus/close agents, update settings/persistence, control hooks/consent/usage bridge, directories, layouts and assets.
6. VS Code terminal/workspace events update agent selection, remove closed agents, and refresh directory lists.
7. Deactivation calls provider disposal; disposal stops the embedded server, runtime, layout/profile watchers and store.

Important distinction: the shared server runtime is constructed before the webview handshake, while several scans/restores are initiated from `webviewReady`.

## 5. Host-specific terminal model

VS Code terminal creation is performed directly in `agentManager.ts` with `vscode.window.createTerminal`; `VscodeTerminalAdapter` only exposes active/all terminals through the core `ITerminalAdapter` contract. It is not the terminal-launch implementation.

The standalone counterpart uses `server/src/terminal/standaloneAgentLauncher.ts` and server-managed PTY/session infrastructure. These are different host execution models sharing some agent lifecycle primitives, not one common terminal implementation.

## 6. Persistence boundaries

- Settings: shared `~/.pixel-agents/config.json`, namespace `vscode`.
- Agent and seat state: `~/.pixel-agents/vscode-state.json` through the namespaced `FileStateAdapter`.
- Layout: shared user-level `~/.pixel-agents/layout.json`.
- Character profiles, directories, hooks preferences and usage bridge: shared server persistence services/config.
- Legacy VS Code `globalState` and `workspaceState`: migration inputs only.

Migration is attempted on every activation and is intended to be idempotent. It verifies writes before clearing most legacy keys; the old hooks-enabled preference is intentionally discarded and its key cleared.

## 7. Main coupling observations

- `PixelAgentsViewProvider` is a large composition/controller class spanning transport, lifecycle, provider settings, UI protocol, assets and host events.
- The adapter imports many concrete server modules instead of depending on a narrow host-facing application service.
- VS Code host-specific behavior is partly registered through module-global setters in `fileWatcher.ts`, connecting this module to server finding F-01.
- Agent lifecycle ownership is split between `AgentRuntime` and `agentManager.ts`; the legacy/local `removeAgent` helper duplicates cleanup responsibilities.
- Provider UI/settings can enumerate `hookProviders`, while agent launch and much of runtime scanning remain Claude-specific.

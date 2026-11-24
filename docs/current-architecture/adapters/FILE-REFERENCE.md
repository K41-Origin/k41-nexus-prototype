# Adapters File Reference

**Baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Inventory:** 8 tracked blobs under `adapters/`; recursive tree response was not truncated.  
**Status convention:** VERIFIED means the complete file was retrieved and statically reviewed, not that runtime behavior was executed.

| File | Status | Responsibility / components | Direct dependencies and effects | Consumers / tests |
|---|---|---|---|---|
| `vscode/extension.ts` | VERIFIED | `activate`, `deactivate`; provider singleton; command/view registration | VS Code API; `FileStateAdapter`; migration; provider. Creates adapter/provider and registers subscriptions. | VS Code extension host; package `main` via esbuild entry. No direct activation test found in audited test inventory. |
| `vscode/PixelAgentsViewProvider.ts` | VERIFIED | `PixelAgentsViewProvider`; webview message handling, server/runtime composition, hook consent/install, directory/settings/assets/layout handling, terminal event callbacks, disposal | VS Code, Node fs/os/path, core palette/provider/state contracts, broad server services. Starts embedded server; watchers/listeners; filesystem writes; Webview messages. | Constructed by `extension.ts`; no dedicated test file found in repository test inventory. |
| `vscode/agentManager.ts` | VERIFIED | `getProjectDirPath`, `launchNewTerminal`, local `removeAgent`, `persistAgents`, `restoreAgents`, `sendExistingAgents`, `sendCurrentAgentStatuses`, `sendLayout` | VS Code terminals/workspaces; Claude provider; AgentStateStore; file watcher/timers; filesystem/JSONL; persistence and palette services. Creates terminals, starts timers/watchers, mutates/persists agent state. | Imported by PixelAgentsViewProvider. No dedicated agentManager test file found. |
| `vscode/constants.ts` | VERIFIED | VS Code global/config keys, view and command IDs; re-exports shared persistence constants | Re-exports from `server/src/constants.ts`; no runtime effects. | extension.ts and PixelAgentsViewProvider.ts. |
| `vscode/hostDirectories.ts` | VERIFIED | `workspaceDirectories` | VS Code workspace folders; returns host directory entries. | agentManager.ts and PixelAgentsViewProvider.ts. |
| `vscode/migrateVsCodeState.ts` | VERIFIED | `migrateVsCodeState`, `deepEqual`, migration key tables | VS Code Memento, StateAdapter, PersistedAgent, layout persistence; writes shared persistence and clears legacy keys; warning UI/logging. | extension.ts; tested by `server/__tests__/migrateVsCodeState.test.ts`. |
| `vscode/uninstall.ts` | VERIFIED | post-uninstall hook cleanup promise chain | Node runtime; provider hook uninstall, usage bridge uninstall, reset hooks config. | Root `package.json` `vscode:uninstall`; built separately by `esbuild.js`. |
| `vscode/vscodeTerminalAdapter.ts` | VERIFIED | `VscodeTerminalAdapter` implementing `ITerminalAdapter` | VS Code active/all terminal API; no process creation or lifecycle ownership. | Registered by PixelAgentsViewProvider via `setTerminalAdapter`; core contract. No dedicated test found. |

## Cross-module references

- Core contracts: `core/src/adapter.ts`, `core/src/schemas.ts`, `core/src/terminalAdapter.ts`, `core/src/provider.ts`, `core/src/paletteUtils.ts`.
- Server runtime: `server/src/agentRuntime.ts`, `agentStateStore.ts`, `server.ts`, `fileWatcher.ts`, `terminal/standaloneAgentLauncher.ts`.
- Persistence: `fileStateAdapter.ts`, `configPersistence.ts`, `layoutPersistence.ts`, `agentPersistence.ts`.
- Tests: `server/__tests__/migrateVsCodeState.test.ts`, `server/__tests__/fileStateAdapter.test.ts`.
- Build/package: root `package.json`, `esbuild.js`.

## Coverage limits

All 8 adapter source files were retrieved. The two relevant migration/persistence test files were inspected. No dedicated tests for terminal launch/restore, provider lifecycle, webview controller, extension activation/deactivation, or uninstall hook were found in the repository test inventory. Test execution was not performed.

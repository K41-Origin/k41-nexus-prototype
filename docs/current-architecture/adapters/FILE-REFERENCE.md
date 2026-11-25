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


## SHA reconciliation snapshot — 2026-11-25

**Active source:** `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Historical adapter baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Result:** 8/8 tracked paths present; 0 added, 0 removed, 0 changed blob SHAs. All current blob SHAs equal the historical baseline. This is file-identity evidence only; it does not upgrade the static audit to runtime verification.

| Path | Current blob SHA | Bytes |
|---|---|---:|
| `adapters/vscode/PixelAgentsViewProvider.ts` | `94d1ab2e1427377e67ff430a852d49863e5e4d87` | 54454 |
| `adapters/vscode/agentManager.ts` | `30020b3e34d2d83fa4536e0d27f55c9e4fe3295c` | 20075 |
| `adapters/vscode/constants.ts` | `c520cb3649fa71ff16d528e170ba6ed5ec734b68` | 2311 |
| `adapters/vscode/extension.ts` | `6877b076b5a9f7117f78985a4923d19bf5f03b45` | 1859 |
| `adapters/vscode/hostDirectories.ts` | `cff2e59404e9f4c42e0611c723b158058920d7ee` | 841 |
| `adapters/vscode/migrateVsCodeState.ts` | `c1c2ca1b5d1b116bcde2130c17b2ab812a635068` | 6193 |
| `adapters/vscode/uninstall.ts` | `14b92dd04d39a3c8c3f2968d078f17f1a0ab63b9` | 1375 |
| `adapters/vscode/vscodeTerminalAdapter.ts` | `b7f7b6019655d76a0c03ed95a24a52e1a0a18b18` | 459 |

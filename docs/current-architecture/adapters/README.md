# Adapters

**Status:** Static source audit completed for this directory; cross-module integration audit remains in progress.  
**Baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`

## Scope

The directory contains one host integration: `adapters/vscode/`. It is not a generic provider-adapter registry. It is the VS Code extension boundary that binds VS Code APIs to shared core/server functionality.

## Documents

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Deep audit](DEEP-AUDIT.md)

## Audit coverage

- 8/8 tracked files inventoried and fetched at the baseline.
- All 8 source files statically reviewed; large files were read in line-range chunks.
- Relevant tests inspected: `server/__tests__/migrateVsCodeState.test.ts`, `server/__tests__/fileStateAdapter.test.ts`.
- Build entry and packaging configuration inspected: root `package.json`, `esbuild.js`.
- Related server runtime seams inspected: `AgentRuntime`, `PixelAgentsServer`, `standaloneAgentLauncher.ts`, `fileWatcher.ts`, persistence and directory modules.
- No test, typecheck, lint, build, VS Code host run, or live Claude session was executed.

## Boundary

This audit describes the existing VS Code host integration. It does not implement or endorse the future Main Agent / Worker architecture. Findings are static observations and require dynamic verification where stated.

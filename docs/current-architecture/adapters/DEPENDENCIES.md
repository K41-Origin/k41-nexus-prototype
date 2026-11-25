# Adapters Dependencies

**Status:** PARTIAL — adapter-to-host/runtime coupling is documented in [DEEP-AUDIT.md](DEEP-AUDIT.md); a complete dependency inventory and build-enforcement audit are not yet complete.

## Evidence already recorded

- The static adapter audit covers 8/8 tracked files at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- `PixelAgentsViewProvider` composes server/runtime, VS Code host APIs, webview transport, hooks/consent, persistence and terminal/workspace events.
- `VscodeTerminalAdapter` exposes VS Code terminal capabilities; VS Code itself owns terminal creation/launch.
- Extension bundle uses the Node platform with `vscode` external; uninstall is a separate plain-Node bundle.
- Findings A-01–A-10 and confirmed constraints are owned by [adapter DEEP-AUDIT](DEEP-AUDIT.md).

## Remaining scope

- Enumerate all direct and transitive imports from adapters to core/server and VS Code host SDKs.
- Classify runtime, development, optional and platform-only dependencies.
- Verify package/bundler externalization and dependency boundary enforcement.
- Trace activation/deactivation and disposal ownership with runtime evidence when authorized.

Do not interpret the 8/8 source review as completion of this dependency map. Dynamic verification remains OPEN.

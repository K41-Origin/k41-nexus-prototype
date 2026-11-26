# System Dependency Map Audit Record

**Status:** STATIC MAP IN PROGRESS — npm lockfile transitive closure mapped (GOV-DEPMAP-002); source import inventory covers 334 code modules and terminal/process ownership is SOURCE-VERIFIED (GOV-DEPMAP-003/004); bundle/metafile comparison, symbol-level call graph and runtime lifecycle remain OPEN. Current verification: `main@f6464594f950b9af49274b8e1724e4d3e96c0271`; source blobs unchanged from the recorded application baseline.

## Owner

[System dependency map](../../SYSTEM-DEPENDENCY-MAP.md) is the sole owner of cross-module edges. This record tracks status only; detailed module findings remain in core/server/webview/adapters/E2E/scripts/CI deep audits.

## Completed static domains

- Compile/build/package edges: AsyncAPI generator, npm workspaces, esbuild outputs, Vite asset metadata, package allowlist and external dependencies.
- Host composition: VS Code extension → FileStateAdapter/PixelAgentsViewProvider → AgentStateStore/AgentRuntime/PixelAgentsServer; standalone CLI → same server/runtime abstractions plus standalone adapter/PTY manager.
- Server/transport: Fastify route registration, hook callback, WebSocket control plane, terminal data plane.
- Client transport: VS Code postMessage and standalone browser WebSocket selected by transport factory.
- Protocol: AsyncAPI YAML → generated ClientMessage/ServerMessage unions.
- Persistence: StateAdapter → FileStateAdapter, host namespaces, shared config and external Claude state.
- Provider: HookProvider contract → provider registry (Claude only) → HTTP hook endpoint → AgentRuntime.
- Trust boundaries: standalone token/Host/origin checks and read-only gate for unprivileged WebSocket clients.
- Source import graph: 334 TS/JS modules across adapters (8), core (27), server (74), webview UI (214) and scripts (11) statically inventoried; cross-layer directions and Node/build-only edges summarized in SYSTEM-DEPENDENCY-MAP.md (GOV-DEPMAP-003).
- Terminal/process ownership: VS Code-owned terminal, standalone in-process PTY and development detached PTY-host modes mapped with source paths/SHAs (GOV-DEPMAP-004).
- CI: workflow-to-script/build/test/release graph including unmerged BUILD-008/009 remediation branch.

## Evidence classes

Every edge in the owning map identifies source commit, source path, line range and blob SHA where source was directly fetched. Existing lifecycle/terminal/persistence edges reused from module docs are labelled DOC-RECORDED. Runtime-only edges remain OPEN. Complete source tree was TREE-VERIFIED (856 entries, `truncated=false`); comparison to main showed only two workflow blob changes.

## Open work

1. Compare the completed static source import map with esbuild/Vite metafiles and actual package/VSIX contents.
2. Resolve only named symbol-level call paths that are needed to explain cross-module behavior; avoid a redundant repository-wide function graph.
3. Runtime lifecycle, reconnect, PTY process, persistence race and multi-process behavior.
4. Actual package, VSIX, OIDC, registry and external service evidence.
5. External GitHub/Environment/ruleset/secret settings.

No tests, build, lint, typecheck, generation, package, CI dispatch or runtime experiment was performed. No application source changed.

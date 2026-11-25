# System Dependency Map Audit Record

**Status:** STATIC MAP IN PROGRESS — major host composition, protocol, persistence, provider, transport and infrastructure edges are SOURCE-VERIFIED; npm lockfile transitive closure is now mapped (GOV-DEPMAP-002); exhaustive source import/call and runtime lifecycle edges remain OPEN. Latest source snapshots: `main@b1104a808cf70382c2bd14a94d372d907a500cda`; remediation branch `security/remediate-build-008-009@1b71acd07b48d56527d0ad72e4d5e23713b390a7`.

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
- CI: workflow-to-script/build/test/release graph including unmerged BUILD-008/009 remediation branch.

## Evidence classes

Every edge in the owning map identifies source commit, source path, line range and blob SHA where source was directly fetched. Existing lifecycle/terminal/persistence edges reused from module docs are labelled DOC-RECORDED. Runtime-only edges remain OPEN. Complete source tree was TREE-VERIFIED (856 entries, `truncated=false`); comparison to main showed only two workflow blob changes.

## Open work

1. Exhaustive transitive import/call graph and bundler output comparison.
2. Detailed terminal PTY process/terminal socket ownership map where module docs do not already provide it.
3. Runtime lifecycle, reconnect, persistence race and multi-process behavior.
4. Actual package, VSIX, OIDC, registry and external service evidence.
5. External GitHub/Environment/ruleset/secret settings.

No tests, build, lint, typecheck, generation, package, CI dispatch or runtime experiment was performed. No application source changed.

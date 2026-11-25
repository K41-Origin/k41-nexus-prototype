# System Dependency Map

**Status:** NOT AUDITED — no complete evidence-backed system map has been published.

**Source snapshot for the next mapping pass:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; recursive tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, 856 entries / 739 blobs / 117 trees, `truncated=false`.

This document is the single owner for cross-module dependency and lifecycle edges. Module audits remain the owners of their file-level findings and source ledgers.

## Required map domains

1. Compile-time imports and package/build boundaries: core, server, webview-ui, adapters, E2E, scripts and `.github/`.
2. Runtime composition and ownership: host startup, runtime construction, provider registration and disposal.
3. Agent/session lifecycle: launch, restore, routing, terminal lifecycle, removal and shutdown.
4. Client-server protocol: contract → producer → transport → consumer → state/persistence → acknowledgement.
5. Provider/hook/consent flow: provider identity, event normalization, authorization and side effects.
6. Persistence ownership: state adapters, namespaces, migration and restart behavior.
7. Terminal/process execution and trust boundaries: PTY, VS Code terminals, child processes, credentials and external tools.
8. External dependencies: network, filesystem, package registry, GitHub Actions, Vercel and other services.

## Evidence rules

Every edge must identify its kind (static import, call/ownership, protocol, configuration or runtime-observed), source path, line range, source commit and blob SHA. Runtime-only edges must be labelled OPEN until observed. Do not infer a runtime dependency solely from package co-location or directory structure.

## Existing evidence to reuse

- Core: [module record](governance/modules/core.md)
- Server: [module record](governance/modules/server.md), [deep audit](server/DEEP-AUDIT.md)
- Webview: [module record](governance/modules/webview-ui.md), [protocol matrix](webview-ui/PROTOCOL-MATRIX.md), [deep audit](webview-ui/DEEP-AUDIT.md)
- Adapters: [module record](governance/modules/adapters.md), [deep audit](adapters/DEEP-AUDIT.md)
- E2E: [module record](governance/modules/e2e.md), [deep audit](e2e/DEEP-AUDIT.md)
- Scripts and CI: [scripts audit](SCRIPTS-AUDIT.md), [GitHub CI audit](github-ci/DEEP-AUDIT.md)

## Next action

Build the map incrementally from existing reviewed evidence and targeted source reads only where an edge is missing. Preserve canonical finding IDs (F-*, A-*, WV-*, E2E-*, BUILD-*); do not duplicate findings in this map. No runtime experiments are authorized by this documentation task.

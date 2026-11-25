# Core Module Overview

**Status:** AUDITED — static source and dependency review.

## 1. Architectural role

`core/` is the shared contract/domain layer between `server/`, `webview-ui/`, and `adapters/vscode/`. It has no direct import from those application layers. Its responsibilities are:

1. Define the bidirectional UI/server control-plane protocol.
2. Define normalized provider events and provider capability interfaces.
3. Define persistence, terminal, and message-transport interfaces.
4. Define shared persisted/wire data shapes.
5. Provide pure utilities for character selection, profile validation/migration, identifiers, path normalization, and terminal frame parsing.
6. Decode and index pixel-art assets for host/build consumers.

It does **not** own server lifecycle, sockets/routes, filesystem persistence implementations, VS Code APIs, React state/rendering, PTY processes, provider-specific hook parsing, or LLM inference/orchestration.

## 2. Internal areas

| Area | Files | Responsibility |
|---|---|---|
| Public export surface | `src/index.ts` | Re-exports selected types/constants from the package |
| Wire protocol | `asyncapi.yaml`, `src/messages.ts` | AsyncAPI source of truth and generated TypeScript unions/interfaces |
| Shared domain contracts | `adapter.ts`, `provider.ts`, `teamProvider.ts`, `terminalAdapter.ts`, `transport.ts`, `schemas.ts` | Cross-layer interfaces and data shapes |
| Protocol constants/framing | `constants.ts`, `terminalFrames.ts` | Shared route/state constants and validated terminal frame formats |
| Character/profile domain | `characterProfiles.ts`, `defaultCharacterProfiles.ts`, `directoryCharacters.ts`, `paletteUtils.ts`, `wardrobeItems.ts` | Profile validation, migrations, identity remapping, palette choice and wardrobe payload validation |
| Small shared utilities | `isRecord.ts`, `localId.ts`, `normalizeProjectPath.ts` | JSON-object guard, local ID generation, stable project path naming |
| Asset pipeline | `assets/*` | Manifest flattening, catalog/index generation, PNG decoding and sprite-sheet conversion |

## 3. Public contracts and main relationships

### Message protocol
`asyncapi.yaml` defines one bidirectional WebSocket channel at `/ws`. `messages.ts` is generated from it and provides the discriminated `ClientMessage` and `ServerMessage` unions. The server interprets client commands and emits server events; webview transports carry those message types. The VS Code PostMessage bridge uses the same logical message contract.

### Provider normalization
A concrete provider implementation lives outside core. It implements `HookProvider`, converts provider-specific raw hook payloads into the normalized `AgentEvent` union, and supplies tool classifications and hook-install operations. The server consumes this contract and maps normalized events into protocol messages/state changes. Optional `TeamProvider` extends team-capable providers with teammate discovery and team metadata semantics. Optional `UsageBridge` describes consented account-usage reporting.

Important domain distinction: `AgentEvent` is a normalized event from an external coding-agent session. It is not a future Main Agent / worker orchestration task model.

### Host abstraction
`StateAdapter` abstracts persisted agents, seats, card order and settings. `ITerminalAdapter` abstracts only terminal-name discovery. `MessageTransport` abstracts UI-to-host messaging and connection lifecycle. Implementations and side effects belong to host layers.

### Character and assets
Character profiles are indexed palettes. `characterProfiles.ts` validates and migrates profile data and supplies stable-ID-based index remapping. `paletteUtils.ts` chooses a palette and repeat for a newly launched visual agent. `directoryCharacters.ts` handles Directory-specific default character selection. `wardrobeItems.ts` validates custom wardrobe payloads. The webview interprets part IDs and composes character visuals; core deliberately does not render them.

Asset build utilities produce furniture catalogs and indexes from manifests. PNG decoders convert source images into pixel-grid arrays used by server/webview asset-loading flows. These are visual/product assets, not model assets.

## 4. Runtime behavior and side effects

| Component | Runtime behavior | Side effects |
|---|---|---|
| Protocol/types/interfaces | Type declarations (except constants) | None |
| Character/profile, palette, path and ID utilities | Deterministic/pure functions except time/random in ID/palette defaults | No I/O |
| Terminal frame codec | JSON encode/parse and structural validation | None |
| Asset manifest/catalog/index builders | Read filesystem manifests/directories | Filesystem reads |
| Asset loader | Read PNG files, decode and log malformed furniture | Filesystem reads and console warnings |
| PNG decoder | Decode buffers using `pngjs`; malformed generic sprites may become transparent fallback, pet decoder returns empty frames | CPU/memory; console warnings |
| `StateAdapter`, `HookProvider`, `TeamProvider`, `MessageTransport`, `ITerminalAdapter` | Interfaces only | Implementations own side effects |

Therefore the statement “core is types-only” is **false for the current tree**. It was true only as an outdated/over-broad comment in `index.ts`; core contains executable runtime utilities and Node filesystem/PNG code under `assets/`.

## 5. Dependency rules

- **VERIFIED:** core source imports no `server/`, `webview-ui/`, or `adapters/` modules in the audited source inventory.
- **VERIFIED:** `core/src/assets/pngDecoder.ts` imports `pngjs`; `core/src/assets/build.ts` and `loader.ts` use Node `fs` and `path`.
- **VERIFIED:** generated `messages.ts` is owned by `core/asyncapi.yaml` and `scripts/generate-messages.ts`.
- **INFERRED:** server is the main runtime consumer of provider and persistence contracts; webview consumes protocol and visual domain contracts; VS Code adapter supplies host-specific implementations. Confirm exact import sites in the layer audits.
- **OPEN:** whether every public export is consumed, mirrored locally, or intentionally reserved for future hosts. Do not delete public contracts solely because static usage tools report them unused.

## 6. Architectural observations / risks (not yet refactoring decisions)

1. The core package is not a single runtime profile: it combines browser-safe pure utilities, Node-only filesystem asset tooling, and shared interfaces. A future multi-client or cross-platform engine may need explicit subpackages/targets or a stricter browser/server split.
2. `index.ts` says “Everything in this package is types-only (no runtime behavior)” while it re-exports constants and the package contains runtime functions. This comment is inaccurate and should be corrected when source changes are authorized.
3. AsyncAPI covers the control plane; terminal byte-stream framing is intentionally a separate data plane in `terminalFrames.ts`.
4. `AgentEvent` and persisted `PersistedAgent` represent Pixel Agents’ observed coding sessions. They should not be reused as the future orchestration engine’s task/worker model without explicit domain analysis.
5. The asset pipeline is a substantial product-specific concern and should not be mistaken for the core of a future LLM runtime.

## 7. Audit limitations

This is a static source/configuration audit. The audit did not execute build, tests, protocol generation, or runtime traces. Exact per-layer consumer import sites and test coverage are recorded as OPEN where not directly verified. The next layer audits should confirm consumers against their source files.

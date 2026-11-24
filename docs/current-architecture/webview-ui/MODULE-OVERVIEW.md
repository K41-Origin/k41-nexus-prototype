# Webview UI Module Overview

**Status:** STATIC ARCHITECTURE DOCUMENTED — detailed file/protocol closure remains in progress.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Runtime entry and composition

- index.html is the standalone SPA document; src/main.tsx mounts React.
- loading.html and src/loadingPreview.tsx provide a development-only loading-scene preview.
- src/runtime.ts distinguishes VS Code webview from standalone browser.
- src/App.tsx composes office, agent cards, layout editor, settings, conversation surfaces, terminal surfaces and mobile navigation.
- src/transport/index.ts creates a module-level singleton: VS Code postMessage, standalone WebSocket, or Vite development mock injection.
- useExtensionMessages coordinates most server-driven office/agent/asset/settings state. Character, usage, conversation and diagnostics messages are delegated.

## 2. Feature boundaries

| Area | Responsibility |
|---|---|
| office | OfficeState, tile/layout domain, workstations, characters/pets, editor tools, canvas renderer and sprite data |
| characters | Cast/profile model, wardrobe manifest/art, character sheet composition, pixel editor and cast persistence |
| terminal | xterm UI helpers, clipboard/selection/link/touch behavior; raw terminal stream is separate |
| mobile | Mobile shell/menu, edge swipe and keyboard viewport |
| music | Seeded procedural composition, song-code generation and Web Audio playback |
| conversations | Recent conversation sidebar and server-backed title search/resume UI |
| usage | Provider-keyed usage bridge state and usage bars |
| components/ui | Reusable pixel-styled controls and primitives |
| hooks | App orchestration, transport-driven state, launch/editor actions and viewport/interaction lifecycle |
| transport | VS Code postMessage and standalone WebSocket adapters plus reconnect policy |

## 3. Domain ownership

- OfficeState owns in-memory office scene state: agent characters, seats, tiles, furniture and related entities.
- EditorState and office editor modules own layout-editing interaction state.
- castStore owns client-side character cast draft/synchronization state.
- usageStore owns provider-keyed usage bridge status/limits in memory.
- Conversation UI owns query/list presentation; server reads transcript sources and supplies results.
- Host/server owns durable layout, settings, agent/session state, character profiles and filesystem-backed data. UI is not the authoritative persistence layer.

See STATE-MANAGEMENT.md for ownership/update paths.

## 4. Rendering model

The office is a 2D pixel-art scene rendered with Canvas 2D, not WebGL/3D. OfficeCanvas coordinates a render loop and renderer using OfficeState, sprites, tiles, furniture and agent/pet entities. Character studio has a separate sheet renderer and pixel-editing canvas. Terminal content is rendered by xterm and is not part of the office canvas.

See RENDERING-ENGINE.md.

## 5. Host integration and data flow

Production asset flow: host/standalone server -> server assetLoader -> core decoders -> LoadedAssets messages -> transport -> useExtensionMessages -> office asset stores/catalog.

Development asset flow: Vite plugin -> core asset build/loader -> development JSON endpoints -> browserMock -> WebSocketTransport.deliver -> useExtensionMessages.

Control-plane messages are described in PROTOCOL-MATRIX.md. Raw terminal frames use a separate transport. VS Code owns native terminals; standalone server owns PTY sessions and the webview terminal drawer.

## 6. Audit status

This overview does not close the 459-file audit. Consult FILE-REFERENCE.md before any further read and DEEP-AUDIT.md for findings, coverage limits and remaining work.

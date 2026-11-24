# Webview UI Module Overview

**Status:** STATIC ARCHITECTURE DOCUMENTED — detailed file/protocol closure remains in progress.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Runtime entry and composition

- index.html is the standalone SPA document; src/main.tsx mounts the React application.
- loading.html and src/loadingPreview.tsx provide a development-only loading-scene preview; they are not the main application entry.
- src/runtime.ts determines whether the UI is running as a VS Code webview or standalone browser.
- src/App.tsx composes the application shells, office scene, agent cards, layout editor, settings, conversation surfaces, terminal surfaces and mobile navigation.
- src/transport/index.ts creates the module-level transport singleton. VS Code uses postMessage; standalone uses WebSocket; Vite development injects browserMock messages into the standalone transport implementation.
- useExtensionMessages coordinates most server-driven office/agent/asset/settings state. Character, usage, conversation and diagnostics messages are delegated to focused modules/components.

## 2. Feature boundaries

| Area | Responsibility |
|---|---|
| office | OfficeState, tile/layout domain, workstations, characters/pets, editor tools, canvas renderer and sprite data |
| characters | Cast/profile model, wardrobe manifest and art, character sheet composition, pixel editor and cast persistence |
| terminal | xterm integration, terminal control UI helpers, clipboard/selection/link/touch behavior; raw terminal stream is separate from control-plane messages |
| mobile | Mobile shell/menu, edge swipe and keyboard viewport behavior |
| music | Seeded procedural composition, song-code generation and Web Audio playback |
| conversations | Recent conversation sidebar and server-backed title search/resume UI |
| usage | Provider-keyed usage bridge state and usage bars |
| components/ui | Reusable pixel-styled controls and primitives |
| hooks | Application orchestration, transport-driven state, launch/editor actions, viewport and interaction lifecycle |
| transport | VS Code postMessage and standalone WebSocket adapters plus reconnect policy |

## 3. Domain ownership

- The UI holds transient interaction and presentation state.
- OfficeState owns the in-memory office scene model: agent characters, seats, tiles, furniture and related scene state.
- EditorState and office editor modules own layout-editing interaction state.
- CastStore owns the client-side character cast draft/synchronization state.
- UsageStore owns provider-keyed usage bridge status/limits in memory.
- Conversation UI owns query/list presentation state; the server reads conversation transcripts and supplies results.
- The host/server owns durable layout, settings, agent/session state, character profiles and filesystem-backed data. The UI sends commands and receives snapshots/acknowledgements; it is not the authoritative persistence layer.

See STATE-MANAGEMENT.md for ownership and update paths.

## 4. Rendering model

The office is a 2D pixel-art scene rendered with Canvas 2D, not WebGL or a 3D engine. OfficeCanvas coordinates a render loop and renderer using OfficeState, sprites, tiles, furniture and agent/pet entities. The character studio has a separate sheet renderer and pixel-editing canvas. Terminal content is rendered by xterm in DOM/canvas-backed terminal infrastructure and is not part of the office canvas.

See RENDERING-ENGINE.md for the render paths and cache/lifecycle findings.

## 5. Host integration and data flow

Production asset flow:
host/standalone server -> server assetLoader -> core decoders -> LoadedAssets messages -> transport -> useExtensionMessages -> office asset stores/catalog.

Development asset flow:
Vite plugin -> core asset build/loader -> development JSON endpoints -> browserMock -> WebSocketTransport.deliver -> useExtensionMessages.

Control-plane messages are described in PROTOCOL-MATRIX.md. Raw terminal frame traffic is a separate transport. VS Code owns native terminals; standalone server owns PTY sessions and the webview terminal drawer.

## 6. Audit status

This overview records the known module structure. It does not close the 459-file audit. Use FILE-REFERENCE.md before further reads and DEEP-AUDIT.md for findings, coverage limits and remaining tasks.

# Webview UI Dependencies

**Status:** STATIC DEPENDENCY MAP — full import graph/bundle verification remains pending.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Direct runtime package dependencies

From webview-ui/package.json:

| Package | Role |
|---|---|
| react / react-dom | React application and DOM renderer |
| @xterm/xterm | Terminal emulator UI |
| @xterm/addon-fit | Fit terminal grid to its host element |

The package declares no direct runtime dependency on server/ or adapters/. The UI imports core contracts and selected core runtime helpers through relative source imports.

## 2. Direct development/build dependencies

| Package/group | Role |
|---|---|
| vite, @vitejs/plugin-react | Dev server and production bundling |
| typescript | Project-reference typecheck/build |
| vitest, allure-vitest | Test runner and optional CI/ALLURE reporting |
| tailwindcss, @tailwindcss/vite | Styling pipeline |
| eslint, typescript-eslint and plugins | Static linting |
| pngjs, @types/pngjs | Build-time core asset processing imported by Vite configuration |
| tsx | TypeScript execution support in project scripts/tooling |

The build script is tsc -b followed by vite build. The test script is vitest run. Neither was executed during this audit.

## 3. Internal dependency boundaries

### UI -> core

The UI imports:
- Message unions and protocol types.
- Character profile and wardrobe-item validation/migration helpers.
- Shared transport contracts and constants.
- Asset sprite types and selected shared utility types.

core/asyncapi.yaml is the source of truth for the control-plane message contract; core/src/messages.ts is generated. The webview should not independently redefine wire payloads.

### Build tooling -> core assets

vite.config.ts imports core asset build/loader utilities for the browser-mock development asset endpoints and build-time asset metadata. tsconfig.node.json includes Vite config, core/src/assets TypeScript and tests. pngjs is therefore a build/development dependency for this pipeline, not evidence that browser runtime decodes PNG through Node.

### UI -> host/server boundary

The browser UI does not directly import server or VS Code adapter implementation modules. It communicates through MessageTransport:
- VS Code: postMessage to PixelAgentsViewProvider.
- Standalone: WebSocket to server /ws.
- Vite development: browserMock injects simulated ServerMessage values.

Raw terminal frames are a separate stream from the control-plane message union.

## 4. Tooling and configuration

- tsconfig.json references app and node projects.
- tsconfig.app.json is strict, targets ES2022 + DOM, and includes src/.
- tsconfig.node.json is strict and includes vite.config.ts, core/src/assets/**/*.ts and test/**/*.ts.
- vitest.config.ts uses Node environment, serial test-file execution and optional Allure output.
- eslint.config.js imports the repository-level eslint-rules/pixel-agents-rules.mjs plugin; this is a cross-directory tooling dependency.
- index.html references the main React entry and public web manifest; loading.html is a separate development preview entry.

## 5. Dependency findings / constraints

- Do not move core asset-processing dependencies into browser runtime without tracing their Node-only imports and bundling boundary.
- Generated messages.ts must not be hand-edited.
- Vite development browserMock and production server asset flow are distinct.
- The module-level transport singleton means importing transport has runtime side effects (transport construction; standalone production connects).
- Exact production bundle composition, tree-shaking and final dependency graph remain unverified because no build/bundle inspection was run.

## 6. Audit boundary

This document records package declarations and source-level boundaries. It does not claim a complete transitive import graph or successful build. No dependency installation, build, lint or typecheck was performed.

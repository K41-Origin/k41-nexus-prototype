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

## 2. Development/build dependencies

Vite and @vitejs/plugin-react provide development server and bundling. TypeScript drives project-reference compilation. Vitest and allure-vitest provide test execution/reporting. Tailwind and @tailwindcss/vite provide styling. ESLint plus TypeScript/React/import-sort plugins provide linting. pngjs and @types/pngjs support build-time asset processing imported by Vite configuration. tsx is available for TypeScript tooling.

The package build script is tsc -b followed by vite build. The test script is vitest run. Neither was executed during this audit.

## 3. Internal dependency boundaries

### UI -> core

The UI imports message unions/protocol types, character profile and wardrobe validation/migration helpers, shared transport contracts/constants, asset sprite types and shared utility types. core/asyncapi.yaml is the protocol source of truth; core/src/messages.ts is generated and must not be hand-edited.

### Build tooling -> core assets

vite.config.ts imports core asset build/loader utilities for browser-mock development endpoints and build-time asset metadata. tsconfig.node.json includes Vite config, core/src/assets TypeScript and test TypeScript. pngjs is therefore a build/development dependency for this pipeline, not evidence of Node PNG decoding in browser runtime.

### UI -> host/server boundary

The browser UI does not directly import server or VS Code adapter implementation modules. It communicates through MessageTransport:
- VS Code: postMessage to PixelAgentsViewProvider.
- Standalone: WebSocket to server /ws.
- Vite development: browserMock injects simulated ServerMessage values.

Raw terminal frames are a separate stream from the control-plane message union.

## 4. Tooling configuration

- tsconfig.json references app and node projects.
- tsconfig.app.json is strict, targets ES2022 + DOM, and includes src/.
- tsconfig.node.json is strict and includes vite.config.ts, core/src/assets/**/*.ts and test/**/*.ts.
- vitest.config.ts uses Node environment, serial test-file execution and optional Allure output.
- eslint.config.js imports the repository-level eslint-rules/pixel-agents-rules.mjs plugin; this is a cross-directory tooling dependency.
- index.html references the main React entry and public web manifest; loading.html is a separate development preview entry.

## 5. Constraints and open questions

- Do not move core asset-processing dependencies into browser runtime without tracing Node-only imports and bundling boundaries.
- Generated messages.ts must not be hand-edited.
- Vite development browserMock and production server asset flow are distinct.
- The module-level transport singleton means importing transport has runtime side effects (construction; standalone production connects).
- Production bundle composition, tree-shaking and final transitive dependency graph remain unverified because no build/bundle inspection was run.

## 6. Audit boundary

This document records package declarations and source-level boundaries. It does not claim a complete transitive import graph or successful build. No dependency installation, build, lint or typecheck was performed.

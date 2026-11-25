# Core Dependencies

**Status:** AUDITED — direct source imports and root workspace configuration reviewed; full consumer graph remains OPEN.

## 1. Architectural dependency rule

The intended rule documented in repository guidance is that core is the lowest application layer and must not import server, webview, or VS Code adapter modules.

**VERIFIED (static source review):** no imports from `server/`, `webview-ui/`, or `adapters/` were found in the audited `core/src` source files.

**Important correction:** core is not dependency-free and not purely types-only. It imports Node built-ins and an external PNG decoder in its asset subsystem.

## 2. Direct dependencies

| Dependency | Used by | Purpose | Runtime/environment |
|---|---|---|---|
| Node `fs` | `assets/build.ts`, `assets/loader.ts` | Read manifests, directories and PNG files | Node.js |
| Node `path` | `assets/build.ts`, `assets/loader.ts` | Construct asset paths | Node.js |
| `pngjs` | `assets/pngDecoder.ts` | Decode PNG buffers | Node.js-compatible package |
| `./messages.js` | provider, transport, directoryCharacters, characterProfiles, wardrobeItems | Generated wire contract types | TypeScript type/runtime import as declared |
| `./schemas.js` | adapter, index | Shared persistence/data shapes | Type-only |
| `./teamProvider.js` | provider | Optional team capability contract | Type-only |
| `./constants.js` | transport, index | Shared route/state/display constants | Runtime |
| `./assets/constants.js` | characterProfiles, characterSheet, pngDecoder, seatedFrame | Pixel dimensions and layout constants | Runtime |
| `./assets/seatedFrame.js` | characterProfiles, characterSheet | Legacy sheet/edit migration | Runtime |
| `./assets/colorUtils.js` | characterSheet, pngDecoder | RGBA-to-hex conversion | Runtime |
| `./assets/characterSheet.js` | pngDecoder | Character sprite slicing | Runtime |
| `./assets/manifestUtils.js` | assets/build | Furniture manifest flattening | Runtime |
| `./assets/types.js` | assets/build, assets/loader, pngDecoder | Asset data shapes | Type-only |
| `./isRecord.js`, `./localId.js` | characterProfiles, wardrobeItems | Validation and local ID helper | Runtime |
| `./directoryCharacters.js` | paletteUtils | Directory default character selection | Runtime |

## 3. Root workspace/toolchain dependencies

The repository root is the package manifest for core; there is no separate `core/package.json` in the audited tree. Root scripts:
- `asyncapi:generate` uses `tsx` and `@asyncapi/modelina`.
- `asyncapi:validate` uses `@asyncapi/parser`.
- `check-types` runs root TypeScript checking and server test-config type checking.
- `lint` includes `core`.
- `format` and `format:check` include `core/src/**/*.ts`.
- `compile` and `package` generate protocol types before type-check/lint/build.

The root manifest lists `pngjs` as a devDependency, not a production dependency. This is an architectural packaging consideration because core's asset decoder imports it at runtime. Confirm bundling/package behavior before changing dependency placement; do not assume it is currently broken.

## 4. Environment boundaries

| Core area | Browser-safe? | Node-specific? | Notes |
|---|---|---|---|
| Protocol/types/interfaces | Yes | No | No Node APIs |
| Character/profile/palette/path/ID utilities | Generally yes | No direct Node imports | ID uses Date.now/Math.random |
| Terminal frame parser | Yes | No | JSON and structural checks |
| Asset constants/color/sheet/seated-frame logic | Yes | No | Typed arrays and pure transformations |
| Asset PNG decoder | No assumption | Yes/Node package | Imports pngjs and Buffer |
| Asset build/loader | No | Yes | Imports fs/path |

The asset folder mixes pure cross-runtime transformations with Node-only filesystem and PNG decoding. Any future browser/Android client must avoid importing Node-only modules into its bundle.

## 5. Consumers and direction

- **Server:** expected to consume provider contracts, StateAdapter, shared schemas, path/palette/profile utilities, asset loading and terminal frame contracts. Exact consumer mapping is deferred to the server audit.
- **Webview:** expected to consume ClientMessage/ServerMessage types, MessageTransport, visual schemas, character/profile/palette/wardrobe and terminal frame contracts. Exact consumer mapping is deferred to the webview audit.
- **VS Code adapter:** expected to implement terminal and persistence abstractions and connect the server to VS Code APIs. Exact consumer mapping is deferred to adapter audit.

These are consumer responsibilities established by interface documentation and repository architecture; exact import locations are not exhaustively verified in this core-only pass.

## 6. Dependency risks and open questions

1. Does the published extension/CLI bundle `pngjs` wherever `pngDecoder.ts` is reached? Root manifest classifies it as devDependency; inspect esbuild external/bundle configuration and package verification before concluding.
2. Should pure browser-safe core functions and Node asset I/O be split into distinct entry points/packages for future standalone/browser/mobile clients?
3. Are any consumers importing deep paths rather than the curated `core/src/index.ts` API?
4. Are local duplicate types in webview intentional compatibility mirrors or drift risks?
5. What automated architecture test enforces the no-upward-import rule? No dedicated core architecture test was identified in this pass.

## 7. Verification status

Static source/config review only. No install, typecheck, lint, build, test, package inspection, or runtime tracing was executed. Do not interpret this document as a passing CI report.

# Webview UI — Deep Static Audit

**Status:** IN PROGRESS — not approved as complete.  
**Repository:** K41-Origin/k41-nexus-prototype  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8  
**Documentation branch:** docs/hybrid-llm-engine-direction  
**Inventory:** 459 tracked blobs; recursive tree validated with truncated=false.

## 1. Audit control and no-duplication rule

FILE-REFERENCE.md records all 459 paths, baseline blob SHA, byte size and the strongest defensible review status known from prior sessions. Before rereading: fetch the current tree and compare blob SHA. If unchanged and marked REVIEWED or REVIEWED REPORTED, do not repeat the same static read; continue only documented cross-references or evidence gaps. If SHA changed, audit the changed content. INVENTORIED, PARTIAL and NOT VERIFIED rows are the remaining targets. Record exact line ranges for large files. Historical conversation notes do not replace line-coverage evidence.

## 2. Inventory

| Scope | Blobs |
|---|---:|
| webview-ui/src | 302 |
| webview-ui/public | 92 |
| webview-ui/test | 54 |
| Root/package/build/config | 11 |
| **Total** | **459** |

Counts exclude directory entries. Recursive tree was not truncated.

## 3. Prior work and findings

### Office

44 files under src/office were previously fetched/reviewed: canvas, overlays, cursor/touch, editor, OfficeState, characters, pets, game loop, renderer, selection, layout serializer/catalog/tile map/workstations, floor/wall/carpet tiles, sprite data/cache, types and utilities. Large files were chunked.
- WV-O01 (Medium): reassignSeat can unassign old seat before validating target; possible inconsistent assignment on failure.
- WV-O02 (Low–Medium): pet-follow comment excludes subagents but implementation does not filter them.
- WV-O03 (Low–Medium): paintTile validates linear index, not row/column separately; reachability pending.
- WV-O04 (Medium): module-global catalog/cache lifecycle and bounds are inconsistent.
- WV-O05 (Performance): ToolOverlay may update React state each animation frame.
- WV-O06 (Low–Medium): DPR changes may be missed when CSS size is unchanged.
- WV-ASSET-03 (Medium): failed furniture catalog rebuild may retain stale module-global catalog.

### Terminal and touch

14 src/terminal files and 2 src/touch files were previously reviewed, with terminal UI integration. Control-plane messages use MessageTransport; raw terminal frames use a separate WebSocket. Terminal reconnect is separate from app reconnect. TerminalConnection.write does not buffer while disconnected. Clipboard OSC 52, URL-to-cell mapping, selection math, touch gestures and detached-DOM rescue listeners have dedicated implementations. Candidate issue: drag listeners/body userSelect may remain if useTerminalDrawer unmounts before mouseup. xterm custom key-handler ownership needs integration verification.

### Character and wardrobe

Character profile/cast/history/wardrobe/renderer logic and major character UI were inspected in prior sessions. Some large UI fetch outputs were truncated; corresponding ledger entries remain REVIEWED REPORTED pending exact range reconciliation.
- wardrobe.json parsed: 15 part categories, 57 parts, 15 palette categories; palette values align with square arrays.
- 81 character PNG paths mapped; no missing required or unregistered art paths.
- All 81 PNG headers have PNG signature and 128×96 IHDR. This is not full CRC/pixel decoding.
- WV-C09: cast persistence is last-writer-wins when local draft remains dirty during external update.
- WV-C10: renderer caches have differing bounds; beard cache growth needs further analysis.
- WV-C11: wardrobe PNGs eagerly inlined/decoded; one decode failure rejects aggregate load.
- WV-WARD-01: rejected wardrobe load Promise remains memoized; no retry.

### Components and hooks

Previously examined component groups include agent cards, launch/settings/directory UI, terminal/mobile UI, character editor, loading scene, tooltips and primitives. Hooks include editor/launch actions, reconnect orchestration, usage bridge, terminal drawer, viewport and intro tour. Exact coverage varies and is represented conservatively in FILE-REFERENCE.
- WV-C01: IntroBubble may update React state each animation frame.
- WV-C02: LoadingScene cache bounds/disposal need analysis.
- WV-C03/WV-C06: global drag listeners rely on mouseup; unmount cleanup needs verification.
- WV-C05: SettingsModal combines local settings, transport, import/export and usage bridge.
- WV-C07: useExtensionMessages is a high-coupling orchestration hook.
- WV-C08: incoming server-message runtime validation is inconsistent.
- WV-APP-01 (Medium–High, static finding): confirmed stale closure. App.tsx creates isEditDirty with dependencies [editor.isEditMode, editor.isDirty] and passes it to useExtensionMessages. The long-lived message subscription reads it in layoutLoaded handling but depends only on getOfficeState; exhaustive-deps is explicitly suppressed. The first callback closure is retained (normally false/false), so later dirty edits may not block external/reconnect layoutLoaded snapshots. Static path verified; runtime reproduction/test not run.

### Mobile, music, conversations, usage and transport

Prior review covered mobile shell/menu, edge-swipe and keyboard viewport; procedural music composer/engine/player/RNG/song-code; conversation list/search/sidebar; usage store/view/bridge; WebSocket/postMessage transports, reconnect helpers and token helper.
Music composition is seeded but noise playback uses Math.random, so full audio output is not deterministic from song seed alone. AudioContext has no explicit close lifecycle found; worker/object URL/timer disposal needs correlation. Usage bridge may retain pending acknowledgement when send fails during disconnect.
- WV-TR-01 (Medium): one WebSocket subscriber exception can abort dispatch to remaining subscribers; browser mock deliver behavior differs.

### Asset/build pipeline

Cross-module review covered core asset build/loader/manifest utilities and transport contracts, server assetLoader, browserMock, Vite config and UI asset consumers.
Production: host -> server assetLoader -> core PNG decoders -> LoadedAssets -> WebSocket/postMessage -> useExtensionMessages -> OfficeState/FurnitureCatalog.
Development: Vite plugin -> core asset build/loader -> decoded JSON endpoints -> browserMock -> WebSocketTransport.deliver -> useExtensionMessages. Browser mock is a development simulator, not production server.
- WV-VITE-01: custom watcher explicitly handles change; add/unlink invalidation remains unverified.
- WV-ASSET-04: module-global colorized carpet cache has no visible capacity/eviction bound; impact unmeasured.

## 4. Configuration and static asset integration

All 11 webview-ui root/package/build/config blobs were read or previously reviewed: package scripts/dependencies, Vite/Vitest configs, ESLint config, TypeScript project references, entry HTML, loading preview HTML, README and ignore rules. No build/lint/typecheck was run. The Vitest config uses Node environment, serial file execution and optional Allure reporting; TypeScript app config is strict and includes src, while node config includes Vite config, core asset TS and test TS. The webview package build script is tsc -b followed by vite build.

The default layout has 21×22 = 462 tiles and 462 tileColors; 36 furniture records have integer coordinates inside grid bounds. This structural check does not validate collisions, tile semantics or runtime deserialization.

## 5. Tests and evidence limits

All 54 webview-ui/test files were previously fetched/read and suite/case inventories recorded. Selected tests were examined in more detail across Office, character/cast, terminal/touch, mobile, music, usage, conversations, launch, reconnect and browser assets. No tests were executed. Inventory/assertion inspection is not a passing test result.

## 6. Remaining work — do not repeat completed reads

1. Compare current webview tree with ledger blob SHAs; inspect only changed files and rows marked partial/inventoried/unverified.
2. Reconcile exact line coverage for large files and any earlier truncated component/hook retrieval.
3. Public asset inventory now records all 60 PNG signatures/IHDR dimensions, JPEG dimensions, ICO entries, 25 furniture manifests, 2 pet manifests, webmanifest icon dimensions and default-layout structural counts. TTF fetch returned empty base64 despite tree metadata size 1,077,160 bytes; font internals remain unverified. Full PNG CRC/pixel integrity and runtime deserialization remain untested.
4. Trace every AsyncAPI ClientMessage/ServerMessage across UI producer/consumer, adapter bridge, server handler/producer, validation, side effects and persistence.
5. Keep raw terminal frame protocol separate from control-plane messages.
6. Correlate OfficeState/React state ownership, asset loading, terminal reconnect and disposal across host lifecycles.
7. Close audit only after all 459 blobs have defensible status and all text files have been fully reviewed.

## 7. Explicit exclusions

- No application source changes, refactors, feature removals or dependency/config changes.
- No tests, build, lint, typecheck or protocol generation executed.
- No runtime, browser, VS Code host or live terminal experiment performed.
- This is a progress record, not a claim that webview-ui audit is complete.

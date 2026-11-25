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

## 8. Incremental continuation — GOV-WEBVIEW-001 (2026-11-25)

### Repository and coverage reconciliation

At governance branch commit `8fc045de9ddae905cc131498d918ddbd64cc8bca`, recursive tree `6c65c2127b2fdf3742f76b317d43e9faccb61506` returned `truncated: false`. The webview subtree contains 459 blobs (302 src, 92 public, 54 test, 11 root/package/config). Compared with baseline commit `4dd14d612fd37d89ffbbc977959f572395c4eec8`, there are 0 additions, 0 removals and 0 changed blob SHAs. The ledger has 459 rows: no missing paths, extra paths or SHA mismatches.

This closes the **inventory/SHA reconciliation task only**. It does not close content-review gaps. The exact status distribution is recorded in FILE-REFERENCE and governance/modules/webview-ui.md. In particular, the 118 reported-review rows still need precise line-coverage reconciliation, 54 tests remain inventoried rather than executed, and the font remains unverified.

### Targeted reconnect/snapshot trace

Relevant source identities at the verification snapshot:

| Path | Blob SHA | Inspected range / purpose |
|---|---|---|
| `webview-ui/src/hooks/useExtensionMessages.ts` | `649025df90edef84d24c40556cee2ea007e15ccf` | reconnect setup and handler ranges 1–90, 250–980; snapshot/layout handling |
| `webview-ui/src/transport/onReconnect.ts` | `030ae419587d5f3b69b78d74cf295811087e2bcd` | full 14-line helper |
| `webview-ui/src/office/engine/existingAgents.ts` | `f23ff3f79f5fc6baf37f2915f8ff6abde107ce35` | reconciliation and stale-agent logic |
| `webview-ui/src/office/engine/officeState.ts` | `1afd778f4c72f1901ef91266e29a89f117e180c7` | `rebuildFromLayout`, `syncAgent`, subagent detach |
| `server/src/clientMessageHandler.ts` | `d7ae8235ee75de63b1716a4b67f27d69023f5d96` | `handleWebviewReady` snapshot ordering |

The standalone server's `handleWebviewReady` sends `existingAgents` at lines 747–752, then `layoutLoaded` at 754–757, then replays agent activity at 759–762. The webview reconnect callback marks `resyncPending` and sends `webviewReady`; `existingAgents` consumes that flag, runs stale-agent/subagent cleanup, and calls `reconcileExistingAgents`. Since the layout has already been loaded in a live reconnecting page, `layoutReadyRef.current` is true. Existing characters therefore take the immediate `syncAgent` path before the new layout snapshot arrives.

### WV-RESYNC-01 — Snapshot seat may not be applied against a stale layout

- **Severity / confidence:** Medium / Medium.
- **State:** OPEN — STATIC-FINDING; not runtime reproduced.
- **Evidence:**
  - `server/src/clientMessageHandler.ts:747–757` — server emits the agent snapshot before the new layout snapshot.
  - `webview-ui/src/hooks/useExtensionMessages.ts:469–500` — reconnect snapshot is reconciled while the already-loaded layout flag is true.
  - `webview-ui/src/office/engine/existingAgents.ts:88–97` — an already-visible agent calls `syncAgent` immediately.
  - `webview-ui/src/office/engine/officeState.ts:647–650` — `syncAgent` only changes seat when the requested seat ID already exists in the current `this.seats`.
  - `webview-ui/src/office/engine/officeState.ts:170–176, 204–239` — the following `rebuildFromLayout` replaces the seat map and preserves each character's current seat when that ID exists in the incoming layout; it does not retain/retry a snapshot seat ID that was absent from the old layout.
- **Trigger / preconditions:** A client reconnects after a layout change; the server snapshot assigns an existing agent to a seat ID that is absent from the client's stale layout, while that client's previous seat ID still exists in the incoming layout.
- **Technical impact:** The agent can remain assigned to its old seat even though the authoritative snapshot names the new seat. This is a client-side state reconciliation risk; no server persistence corruption is established.
- **Test evidence:** `existingAgents.test.ts` checks the reconciliation decision with a fake office; `subagentResync.test.ts` checks `syncAgent` when both seat IDs already exist. Neither inspected test covers a requested seat absent from the pre-snapshot layout but present in the incoming layout. Tests were read, not executed.
- **Remaining uncertainty:** Confirm reachability across all layout/seat persistence flows and determine whether a later message corrects the assignment. A focused integration test or authorized runtime reproduction is needed before treating the behavior as reproduced.

### Next continuation point

Continuation pointer superseded by GOV-WEBVIEW-003 below. Preserve the historical 118-row count as the state at GOV-WEBVIEW-001; the current ledger is authoritative for remaining coverage.


## 9. Incremental static review — GOV-WEBVIEW-002 (2026-11-25)

### Source review and ledger advancement

- Repository source branch: `main`, commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive tree: `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated: false`.
- Webview subtree: 459 blobs; exact path/blob-SHA comparison with the documentation branch and the canonical baseline found 0 additions, 0 removals and 0 changed SHAs.
- Re-fetched and read the complete content of 55 source files in this session. Of these, 53 rows were advanced from reported-review status to `REVIEWED`; two already-`REVIEWED` terminal data-plane files were revisited for the lifecycle question below. Exact path/SHA/size records remain in `FILE-REFERENCE.md`.
- Large-file retrieval was segmented and covered the complete files: `App.tsx` 1–1031; `useExtensionMessages.ts` 1–997; `CharactersView.tsx` 1–448; `PixelEditor.tsx` 1–471; `Stage.tsx` 1–297; `TerminalPane.tsx` 1–270; `useTerminalDrawer.ts` 1–241.
- Five test files were re-fetched and their complete assertions inspected: `existingAgents.test.ts`, `subagentResync.test.ts`, `onReconnect.test.ts`, `castSync.test.ts`, and `characterRenderer.test.ts`. They remain `TEST-INVENTORIED`; no test was executed.

### Documentation comparison

The reviewed implementation supports the existing architecture documents' main boundaries:
- character cast is an external store with saved/draft state and server synchronization;
- character sheets are composed from wardrobe art and profile edits, then converted to office sprites;
- UI canvas rendering and DPR snapping are separate from office scene rendering;
- terminal control-plane session state is separate from per-agent raw terminal WebSockets;
- desktop keeps hidden xterm panes mounted while mobile mounts only the selected pane;
- reconnect snapshots are reconciled by `useExtensionMessages`, not by transport alone.

No source/document contradiction requiring a new finding was established in this pass. Existing `WV-APP-01`, `WV-TR-01`, `WV-WARD-01`, cache findings and `WV-RESYNC-01` remain open as previously recorded.

### Lifecycle evidence expansion — WV-C03 / WV-C06

- `webview-ui/src/hooks/useTerminalDrawer.ts:150–168` installs window-level `mousemove` and `mouseup` listeners and sets `document.body.style.userSelect = 'none'` during drawer resize.
- The listeners and body style are restored only in the local `onUp` callback. The hook has no effect cleanup for an unmount or lost mouseup before that callback.
- This is a static lifecycle gap under the precondition that the app/hook is torn down or the mouseup is not delivered during an active resize. It is recorded as additional evidence for the existing global-drag cleanup concerns `WV-C03/WV-C06`, not as a duplicate finding ID. Runtime manifestation and exact impact remain unverified.

### Reconnect test coverage cross-check

- `existingAgents.test.ts:64–216` covers layout-ready vs buffered restoration, deduplication, server look/seat synchronization when the requested seat exists, mixed rosters and headless flags.
- `subagentResync.test.ts:16–110` covers stale subagent removal, preserving live subagent position, stale agent filtering, and `syncAgent` when both old and requested seat IDs exist.
- `onReconnect.test.ts:1–64` covers first-connect suppression, subsequent reconnect callbacks and unsubscribe.
- The inspected assertions do not cover the `WV-RESYNC-01` precondition where the requested seat is absent from the pre-reconnect seat map but appears in the incoming layout. This remains a test-coverage gap, not a test failure.

### Updated coverage and limits

- 53 previously reported source-review rows now have complete content retrieval and static review recorded.
- 65 reported-review source rows remain for exact content/line coverage reconciliation.
- 5/54 test files have complete assertion review recorded; 49 remain inventoried only.
- 143 binary rows retain header/metadata-only evidence; the TTF remains `NOT VERIFIED`.
- No tests, build, lint, typecheck, protocol generation, browser/VS Code runtime or reconnect experiment was executed. No application source or asset was modified.


## 10. Incremental static review — GOV-WEBVIEW-003 (2026-11-25)

### Repository and scope

- Source branch: `main`, commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive tree: `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated: false`.
- `webview-ui/`: 459 blobs; selected source/test blob SHAs match the existing ledger. No source changes were found in the inspected paths.
- Documentation branch parent before this session: `d15dbb41666b2ac7420e4ebe88456c250c10298f`.
- Scope: browser-mock bootstrap and asset decoding, Vite asset middleware/cache invalidation, and the two directly related asset-path integration tests.

### Files fully re-fetched and reviewed

| Path | Blob SHA | Lines | Result |
|---|---|---:|---|
| `webview-ui/src/browserMock.ts` | `335caac9b428de7801b0c5c680eaab2dd7a1f290` | 1–248 | Full static review; ledger advanced to REVIEWED |
| `webview-ui/src/main.tsx` | `223bbf51073edc73b9646be0e9618b11ea1ac250` | 1–27 | Full static review; ledger advanced to REVIEWED |
| `webview-ui/vite.config.ts` | `b47531773bf6fea57fe6d46a6291133371aa56ea` | 1–126 | Full static review; ledger advanced to REVIEWED |
| `webview-ui/test/dev-assets.test.ts` | `44c181de7e7f63904143d6e7ccdf3b0fc33bb0b7` | 1–99 | Full assertions inspected; TEST-INVENTORIED |
| `webview-ui/test/build-subpath.test.ts` | `6531f84f343232b656cd9265d701a478babd4277` | 1–137 | Full assertions inspected; TEST-INVENTORIED |

Cross-reference reads (not counted as new webview ledger rows):
- `core/src/assets/loader.ts` — `0cbd192c01398fa1f62fcdafd67f85a0882a1266`.
- `core/src/assets/build.ts` — `08d04b03d8937558c437dc3de7a9dab06568aad3`.

### Source-verified flow and observations

1. `main.tsx:11–16` imports and awaits `initBrowserMock()` only when `isBrowserRuntime && import.meta.env.DEV`; the React root is mounted afterward. `App.tsx` dispatches the mock messages from its effect, while `useExtensionMessages` registers the transport subscription. The second dynamic import resolves the already-loaded module asynchronously, allowing the effect flush to register the listener before dispatch.
2. `browserMock.ts:132–201` fetches the asset index and furniture catalog, prefers decoded floor/wall/furniture JSON in DEV, and falls back to browser PNG decoding if that complete trio is unavailable. Carpet data is fetched separately and degrades to an empty array when its optional endpoint is unavailable.
3. `browserMock.ts:204–248` dispatches the initial mock state in a fixed order, with `furnitureAssetsLoaded` before `layoutLoaded`. This order is consistent with the message handler's dependency on the dynamic furniture catalog before layout application.
4. `vite.config.ts:53–100` exposes the metadata and decoded JSON middleware and memoizes decoded values in a plugin-local cache. Cache invalidation currently subscribes to watcher `change` only. This is additional source evidence for existing `WV-VITE-01`; add/unlink invalidation remains unverified. No duplicate finding ID created.
5. The source comment in `browserMock.ts` describes fallback behavior for plain browser builds, but the actual entrypoint gate in `main.tsx:11–16` only imports the module in Vite DEV. Therefore the PNG fallback is statically reachable through this entrypoint only in DEV when one or more required decoded endpoints are unavailable; it is not established as a production static-browser path.
6. `dev-assets.test.ts` checks root and `/sub/` endpoint availability, index/catalog contents, and representative floor/wall/furniture/default-layout URLs. It does not request `decoded/carpets.json`, nor does it exercise the browserMock fallback/dispatch behavior or watcher invalidation.
7. `build-subpath.test.ts` checks production JS/CSS, metadata sidecars and manifest icon URLs under `/sub/`; it does not boot the browser application or assert browserMock dispatch. The production path does not invoke browserMock through the inspected `main.tsx` gate.

### Findings and test limits

- No new defect finding was established in this pass.
- Existing `WV-VITE-01` remains OPEN: the plugin's change-only watcher is statically verified; the runtime effect of add/unlink events is not tested.
- The two asset-path tests are now TEST-INVENTORIED, not TEST-EXECUTED. Their assertions leave the carpets endpoint, browser PNG fallback, mock message order, and watcher add/unlink behavior without direct coverage in these files.
- No tests, build, lint, typecheck, protocol generation, browser runtime or file-watcher experiment was executed.
- No application source, assets, configuration or dependencies were modified.

### Updated coverage

- Source files: 199 REVIEWED; 62 reported-review source rows remain.
- Tests: 7/54 complete assertion inspections recorded; 47 remain inventory-only.
- Binary evidence and TTF status are unchanged.
- Inventory remains 459/459; this session does not close the webview audit.

### Next continuation point

Continue from the remaining 62 reported-review source rows by reconciling their historical line-level evidence, then review the 47 remaining test files as needed. Keep protocol producer/consumer validation and the open WV findings in scope; do not reread unchanged files already fully reviewed for the same question.

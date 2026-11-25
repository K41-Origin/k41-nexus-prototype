# Webview UI — Deep Static Audit

**Status:** IN PROGRESS — not approved as complete.  
**Repository:** K41-Origin/k41-nexus-prototype  
**Historical baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8  
**Latest source verification:** `main@b1104a808cf70382c2bd14a94d372d907a500cda` (tree `97940dabd84264b6281917b11928ebae6156752c`, `truncated=false`).  
**Canonical documentation branch:** `main`.  
**Inventory:** 459 tracked blobs; 459/459 current path/blob SHAs match.

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



## Binary integrity continuation — GOV-ASSET-CRC-001 (2026-11-26)

**Verification source:** `main@b1104a808cf70382c2bd14a94d372d907a500cda`; complete recursive tree `97940dabd84264b6281917b11928ebae6156752c`, `truncated=false`. Webview subtree: 459 blobs; 459/459 path/blob-SHA pairs match this ledger.

- **PNG:** 141 tracked PNG paths represent 132 unique blob SHAs. Every unique PNG blob was fetched as base64 and checked for byte-size agreement, PNG signature, bounded chunk lengths, IHDR/IDAT/IEND presence, and CRC-32 for every chunk. All 132 unique blobs passed those checks. Duplicate paths share the same verified blob SHA.
- **One non-canonical tail:** `webview-ui/public/assets/pets/gitcat/pet.png` has two trailing zero bytes after a valid IEND chunk. All chunk CRCs pass. This is recorded as an observation, not a reproduced rendering defect.
- **ICO:** `favicon.ico` directory bounds are valid; all three embedded PNG images (16×16, 32×32, 48×48) have valid signatures, bounded chunks, IEND and chunk CRCs.
- **JPEG:** `Screenshot.jpg` has valid SOI, baseline SOF0 (1308×521, 3 components), SOS and EOI markers; byte size matches the tree.
- **Still not full decode:** this execution environment has no zlib/PNG/JPEG decoder or local repository checkout. PNG IDAT streams and JPEG entropy-coded pixels were not decompressed/rendered. Therefore this evidence is stronger than header-only validation but does not claim pixel-level decode or browser loading.
- **TTF:** the font blob is present in the current tree (1,077,160 bytes; SHA `767a03d1f7417bbfdde1b42ce580b7dcdbd2cc9e`), but the GitHub connector returned empty base64 for this >1 MB blob. SFNT table directory, checksums, name tables, cmap/glyph coverage and font parser validation remain OPEN.

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


## 11. Historical reported-review reconciliation — GOV-WEBVIEW-004 (2026-11-25)

### Governance and repository state

- Governance documentation reviewed before source/ledger reconciliation: governance README, audit register, audit method, evidence log, webview module record, webview README, DEEP-AUDIT and FILE-REFERENCE.
- Source branch/baseline: `main`, commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive Git tree response: `truncated: false`; 459 `webview-ui/` blob entries.
- Documentation branch before this session: `docs/hybrid-llm-engine-direction`, latest known commit `2614502f8983977ed850c1a8a9b840689c6aa183`.
- The 62 reported-review ledger paths were compared against the source tree. All 62 paths exist and all 62 blob SHAs match the ledger. No changed or missing path was found in this subset.

### Historical evidence inspected

The historical DEEP-AUDIT records broad subsystem-level claims, not a per-file line-range manifest:
- 44 files under `src/office` had been fetched/reviewed, with large files chunked.
- 14 `src/terminal` and 2 `src/touch` files were reported reviewed.
- Character profile/cast/history/wardrobe/renderer logic and major character UI were examined, but the audit explicitly says some large UI fetch outputs were truncated and corresponding ledger entries remain `REVIEWED REPORTED`.
- Component and hook groups are named broadly, while the same document says exact coverage varies and is represented conservatively in FILE-REFERENCE.

Git history for FILE-REFERENCE was also checked across commits `1165d32dc85fe578b3e2a9feb137bf524b3953ad`, `07ca3b67e0640aee1a93af5f1c48e5445695a47f`, `13354f1a74b75b9ab3d8982544414134b1940ae3`, `65502c86523bca71625a86194b898e3c8b84182a` and `d15dbb41666b2ac7420e4ebe88456c250c10298f`. The reported-review statuses and their absence of file-specific line ranges persist across those ledger versions. The commit titled `close component tail coverage gaps` changed audit documentation only; its ledger diff did not provide per-file line-range evidence for the 62 rows.

### Reconciliation result

| Ledger group | Rows | Reconciliation |
|---|---:|---|
| `src/characters/ui/` | 9 | Historical subsystem review reported; exact per-file line coverage not recoverable |
| `src/components/` | 32 | Prior component inspection reported; exact per-file range/cross-reference evidence not recoverable |
| `src/hooks/` | 14 | Prior hook review reported; lifecycle/cross-module follow-up and exact per-file coverage remain |
| Other source files (`changelogData.ts`, `constants.ts`, `index.css`, `loadingPreview.tsx`, `notificationSound.ts`, `runtime.ts`, `testHooks.ts`) | 7 | Historical review claim recorded; no exact per-file line-range evidence recovered |
| **Total** | **62** | **All SHA-reconciled; none can safely be promoted to REVIEWED from historical documentation alone** |

This is a completed **historical evidence reconciliation**, not a completed content review of the 62 files. No claim is made that all 62 files were re-fetched or re-read in GOV-WEBVIEW-004. Their ledger statuses remain reported-review and have been annotated to preserve this distinction. Do not downgrade them to INVENTORIED: historical inspection is documented, but its exact coverage is insufficiently evidenced for REVIEWED.

### Consequence and next action

- 39 rows remain `FETCHED / REVIEWED REPORTED`.
- 23 rows remain `REVIEWED REPORTED`.
- All 62 have unchanged source blob SHAs at the selected source baseline.
- Next source-review work must target the missing evidence: fetch and inspect only those files/ranges whose complete prior coverage cannot be established, record exact line ranges and cross-references, then promote only verified rows.
- No source code was modified. No tests, build, lint, typecheck, or runtime experiment was executed.


## 12. Incremental static review — GOV-WEBVIEW-005 (2026-11-25)

### Governance and repository state

- Governance documents reviewed before source/documentation updates: governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, webview README, DEEP-AUDIT and FILE-REFERENCE.
- Documentation branch: `docs/hybrid-llm-engine-direction`, starting commit `9c64638df8d4048f404e62193a3fbe1327d88252`.
- Source branch: `main`, commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive source tree: `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated: false`.
- The `webview-ui/src/components/` subtree contains 54 blobs; all 54 paths are represented in FILE-REFERENCE and all component blob SHAs match the ledger.
- Scope of this session: direct full-content review of the 32 component rows that GOV-WEBVIEW-004 had left as reported-review due to missing historical per-file line evidence. The other 22 component files already had REVIEWED evidence and were not repeated.

### Files fully re-fetched and statically reviewed

| Path | Blob SHA | Lines | Result |
|---|---|---:|---|
| `webview-ui/src/components/directoryStrip.ts` | `fe4746c500da2a7ae091324e2f8232dd429655c9` | 1–46 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/introBubbleGeometry.ts` | `e8750b34e3459ee3d1ecc0493a473424b2898012` | 1–109 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/loadingSceneArt.ts` | `2d33cd9f1fad94103f20e22622c3dd58a88bba16` | 1–97 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/loadingSceneLayout.ts` | `a122bbff64ef37b31bbc3daf046a977b01ba0f9f` | 1–223 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/MigrationNotice.tsx` | `0aa1bd3190d5a50d7a20965d9b3250399a90e319` | 1–35 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/MobileAgentBar.tsx` | `6fc6be39ffb350cfd8090e0252c89f9cb7c7a9e2` | 1–196 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/MobileKeyBar.tsx` | `d3a0d3f03d998577feacc056b6edf458ba456ddc` | 1–196 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/statusDot.ts` | `23c78e9f495dbcdfc9031f37f95776f8d337d9dc` | 1–11 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/TerminalHideRibbon.tsx` | `54f35f46fb05adb34c75c6fddeca595f3dceffa9` | 1–52 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/Tooltip.tsx` | `5e2305434b2eb22075dea27fccec0324a942bde6` | 1–39 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/Button.tsx` | `0b04f4fef6ce11ef28cf18b19e05a491fe6432a1` | 1–57 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/Checkbox.tsx` | `8688f09731c4456cd7984a29131ebe3df7639722` | 1–57 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/ColorPicker.tsx` | `b4e28664298b0e09e1ec36e3b3292c7e6adaf6e1` | 1–159 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/Dropdown.tsx` | `6bc0dbb17dbb81c7f390e8535a891827f4fd6d1a` | 1–82 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/ItemSelect.tsx` | `f7fa506384023e46c99f0ae333a536c9bde1c25e` | 1–54 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/MenuItem.tsx` | `db9a2cd11f5c48828b8cf75af1f284e70198a816` | 1–21 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/Modal.tsx` | `527899e2657f222d4fd9c706d2414c35097ff364` | 1–92 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/PickArea.tsx` | `6ac096760f5d741376aaa5f82e0ca7d5baa9f8d4` | 1–45 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/pickCanvas.ts` | `7e8bbee9c3a7d4709f6494e9c1d85ae30e60d823` | 1–22 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/pixelFont.ts` | `61e380a9b24a55f20aa4d6676cb58e73de73223d` | 1–128 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/pixelGrid.ts` | `503040e24020218b4bd198019fcc1101c4c5c891` | 1–72 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/PixelIcon.tsx` | `62cc03bdc5f29d020c6592b011c6353d5abe051e` | 1–48 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/pixelIcons.ts` | `cc824e3123c1e78a4f0df461505904804a8d6c50` | 1–339 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/PixelInputText.tsx` | `94e7c2b2ed79e2831cdedd20ad3b835530860321` | 1–34 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/pixelSnap.ts` | `93948494c9ea9f9feb519a49c89e5dfcfed007c0` | 1–25 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/PixelSvg.tsx` | `3eb7a942f1ea8d4a7a56d33e8629f3b0df1954a7` | 1–42 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/PixelText.tsx` | `cd68c015f2b0711633e587a885a5a94c2613c607` | 1–58 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ui/types.ts` | `f83803bc75e7e3fa75a5d32af352bf4974d4ba7d` | 1–14 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/useCardReorder.ts` | `42092b1e069eaa60df8e88f097ba48fa4e4cef0c` | 1–224 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/VersionIndicator.tsx` | `ba5a0c3009bfb6f3028a4a5e786d5410eac5c4be` | 1–126 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/VisualColorPicker.tsx` | `f24cb4e20c3631ae740fa7c559d716b800213690` | 1–254 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |
| `webview-ui/src/components/ZoomControls.tsx` | `7d3e17ed65d2e533b7e452b680c6f549eea947e5` | 1–156 | Full content re-fetched and statically reviewed; ledger promoted to REVIEWED |

This promotes the 32 previously reported component rows to REVIEWED. Together with the 22 previously reviewed component rows, the component directory is now **54/54 REVIEWED**. This is source-content coverage, not runtime or behavioral certification.

### Cross-component paths considered

- Agent-card rendering, selection, status and ordering: `AgentCard`, `AgentCardBar`, `AgentCardList`, `cardBar`, `statusDot`, `useCardReorder`.
- Mobile launch and roster interaction: `MobileAgentBar`, `LaunchDrawer`, directory and character picker components.
- Terminal lifecycle and mobile input: `TerminalPane`, `TerminalPaneStack`, `TerminalDrawer`, `MobileTerminalPage`, `MobileKeyBar`.
- Modal and settings consumers: `Modal`, `SettingsModal`, `DirectoryModal`, `ChangelogModal`.
- Intro and loading render paths: `IntroBubble`, `introBubbleGeometry`, `LoadingScene`, `loadingSceneArt`, `loadingSceneLayout`.
- Shared visual primitives: all 18 files under `components/ui/`.

### Static findings and future technical priorities

#### WV-C12 — Touch cancellation shares the touch-end completion path

- **Status:** STATIC-FINDING / OPEN; runtime manifestation not reproduced.
- **Affected file:** `webview-ui/src/components/useCardReorder.ts`.
- **Evidence:** lines 150–162 define `onTouchEnd`; the handler commits a drag through `drop()` at 154–157 or invokes `onTouchTap` at 159–161. Lines 164–170 register the same handler for both `touchend` and `touchcancel`.
- **Technical concern:** a cancelled gesture is treated as a completed gesture. Depending on the current phase, cancellation can commit a reorder or invoke the tap callback.
- **Impact:** potential unintended card selection/reordering in touch interactions; the mobile consumer uses `onTouchTap` to preserve terminal keyboard focus (`MobileAgentBar.tsx:86–91, 178–189`).
- **Future priority:** distinguish cancellation from successful touch completion and add assertion coverage for cancellation in pending and dragging phases.
- **Uncertainty:** no browser/mobile runtime reproduction or test execution.

#### WV-C13 — Modal dialog semantics and focus lifecycle need verification

- **Status:** STATIC-FINDING / OPEN; accessibility behavior not runtime-tested.
- **Affected file:** `webview-ui/src/components/ui/Modal.tsx`.
- **Evidence:** lines 24–41 implement stacked Escape handling; lines 57–88 render backdrop, wrapper and panel. The panel element at 75–77 has no visible `role="dialog"` or `aria-modal="true"`; this component also contains no focus-trap, initial-focus or focus-return logic.
- **Technical concern:** visual modal behavior and Escape handling do not by themselves establish accessible dialog semantics or keyboard focus containment.
- **Impact:** keyboard and assistive-technology users may not receive a correctly announced or contained modal interaction.
- **Future priority:** assess dialog semantics, focus entry/containment/return and background interaction across all Modal consumers.
- **Uncertainty:** consumer-level focus behavior and browser/screen-reader behavior have not been runtime-verified.

#### WV-C14 — Loading-art rejection has no local recovery path

- **Status:** STATIC-FINDING / OPEN; failure path not runtime-reproduced.
- **Affected files:** `webview-ui/src/components/loadingSceneArt.ts`, `webview-ui/src/components/LoadingScene.tsx`.
- **Evidence:** `loadingSceneArt.ts:61–65, 77–95` caches the load Promise in module scope and aggregates required decodes; `LoadingScene.tsx:118–124` attaches only a fulfillment handler to `loadLoadingArt()`.
- **Technical concern:** a rejected decode Promise remains cached and is not handled by the component. A later call reuses the same rejected Promise.
- **Impact:** loading art can remain unavailable for the lifetime of the module after a required asset decode fails, with an unhandled rejection and no component-level fallback visible in the inspected path.
- **Future priority:** assess an explicit error/fallback state and retry/cache-reset policy.
- **Uncertainty:** actual asset failure behavior and host-level error handling were not exercised.

#### Additional evidence for WV-C03 / WV-C06 — card reorder mouse-listener cleanup

- **Status:** STATIC-FINDING evidence expansion; existing finding IDs retained.
- **Affected file:** `webview-ui/src/components/useCardReorder.ts`.
- **Evidence:** lines 175–203 attach `mousemove` and `mouseup` to `window`; removal occurs inside `onUp` at 188–190. The unmount cleanup at 205–210 clears only the pending timer.
- **Technical concern:** if the component unmounts before `mouseup`, the active window listeners are not removed by the hook cleanup.
- **Future priority:** assess listener ownership and cancellation cleanup. This extends WV-C03/WV-C06; no new duplicate ID is created.
- **Uncertainty:** runtime manifestation and host teardown conditions are not reproduced.

### Updated coverage and execution limits

- Component source coverage: **54/54 REVIEWED** (22 previously reviewed; 32 directly re-fetched and reviewed in this session).
- All 54 component paths and blob SHAs match FILE-REFERENCE at the selected source tip.
- No application source, assets, dependencies or configuration were modified.
- No tests, build, lint, typecheck, protocol generation, browser/VS Code runtime or accessibility experiment was executed.
- All four technical priorities above remain audit observations/open evidence, not implementation tasks performed in this session.

### Next continuation point

Continue webview source audit with the remaining **30 reported-review rows**: 14 hooks, 9 character UI files and 7 other source files. After source coverage, continue test assertion review (47 inventoried-only files), ClientMessage/ServerMessage producer-consumer tracing, host lifecycle/reconnect verification and outstanding binary/font evidence. Do not repeat the 54 component files unless a source SHA changes or a specific cross-module question requires it.


## 13. Incremental static review — GOV-WEBVIEW-006 (2026-11-25)

**Source baseline:** `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Scope:** all `webview-ui/src/hooks/` tracked source files.

### Coverage and SHA reconciliation

- The complete source tree contains **24 hook files**.
- All 24 paths and blob SHAs reconcile exactly with `FILE-REFERENCE.md`: no missing path, extra path or SHA mismatch.
- Ten hook files were already `REVIEWED` from GOV-WEBVIEW-002 and were not redundantly re-reviewed.
- The remaining 14 `REVIEWED REPORTED` files were individually re-fetched in full and statically reviewed in this session. The ledger now records all 24/24 hooks as `REVIEWED`.
- No source SHA changed from the recorded ledger baseline.

### Newly verified hook files

| File | Blob SHA | Lines |
|---|---|---:|
| `webview-ui/src/hooks/useDismiss.ts` | `0910f0925e6afa283fed7ca25895ff19eea22e80` | 25 |
| `webview-ui/src/hooks/useEditorActions.ts` | `386b839a124ffad437c8a3cd9c3460e2826c0d00` | 798 |
| `webview-ui/src/hooks/useEditorKeyboard.ts` | `44ccc408d1a264b45e99cb2c1056650910d3c8c7` | 108 |
| `webview-ui/src/hooks/useFurnitureColorActions.ts` | `8fbaa9bc141aaf2daeabc809da7132e6b1e36519` | 102 |
| `webview-ui/src/hooks/useHoverPin.ts` | `ed9f946d644ba8133beaec95e307b95136127304` | 50 |
| `webview-ui/src/hooks/useIntroTour.ts` | `4fc18a2eef5bb2b73bcf6a4bf5facfc17d25a28d` | 92 |
| `webview-ui/src/hooks/useIsMobile.ts` | `6325a2b4dceaba556eaf113e1121dce279b8e93e` | 36 |
| `webview-ui/src/hooks/useKeyboardViewport.ts` | `7263411c15fa91fef5f4e944ea938b214ea3293e` | 93 |
| `webview-ui/src/hooks/useLaunchActions.ts` | `88d01bb2f03bade7f4f8030e25a34e90687bfbca` | 31 |
| `webview-ui/src/hooks/useLongPress.ts` | `fb7c29dcdc42d2369d9237f654a2bb245aaa30d9` | 70 |
| `webview-ui/src/hooks/usePageBackground.ts` | `7bd272f4950a26b3908d4ae238ac9055459d44a6` | 23 |
| `webview-ui/src/hooks/usePresence.ts` | `141a990f9c8ac9047b621b11e86ced25af7c3dfc` | 40 |
| `webview-ui/src/hooks/useRosterLaunch.ts` | `4988aca1cecc1b3f2f94de7fb2530449af4e567a` | 133 |
| `webview-ui/src/hooks/useSlidePanel.ts` | `2cf4ab0013cae1d82615759e51844e9f02bb7b98` | 79 |

### Cross-reference observations

- `App.tsx:365–375` enables `useEditorKeyboard` whenever edit mode is active and the Characters view is closed. The listener is attached to `window` and does not exclude editable event targets.
- `office/editor/EditorToolbar.tsx:734–744` and `:818–831` render text inputs for adding and renaming Areas while the editor toolbar is present. Their key events bubble to the window listener.
- `useEditorActions.ts` owns edit/undo/redo/save callbacks and debounced transport persistence; `useFurnitureColorActions.ts` composes the color-pick state transitions. No runtime or transport timing was exercised.
- `useKeyboardViewport` delegates keyboard classification to the pure `mobile/keyboardViewport.ts`; the existing `keyboardViewport.test.ts` covers helper samples, not the hook's browser event lifecycle.
- `useIntroTour` drives the pure `introTourState.ts` reducer; `introTour.test.ts` inventories reducer invariants, not React effect/wire integration.
- Launch-menu state and launch-latch behavior are split into pure reducers/helpers (`launchMenus.ts`, `launchLatch.ts`); existing launch-latch tests do not execute `useRosterLaunch` or terminal lifecycle behavior.

### New static findings

#### WV-C15 — Editor shortcuts also receive key events from text inputs

- **Severity:** Medium. **Confidence:** High.
- **Evidence:** `webview-ui/src/hooks/useEditorKeyboard.ts:16–20, 58–79` installs a `window` keydown listener while `isEditMode` is true and dispatches Escape, Delete/Backspace, R/T and undo/redo actions without checking `event.target` or editable state. `webview-ui/src/App.tsx:365–375` activates this hook during editor mode. `webview-ui/src/office/editor/EditorToolbar.tsx:734–744, 818–831` contains text inputs for Area creation and renaming.
- **Technical explanation:** keyboard events from those inputs bubble to `window`; typing Backspace/Delete can invoke deletion of a selected furniture item, R/T can invoke editor actions, and Escape can also advance/close the editor state while the input handles its own Escape behavior.
- **Impact:** unintended layout mutation or editor-mode changes while the user is editing text in the Area controls.
- **Verification:** `SOURCE-VERIFIED` / `STATIC-FINDING`; not runtime reproduced and no test executed.
- **Remaining uncertainty:** exact user-visible outcome depends on focus, selection and active tool at the time of the key event.

#### WV-C16 — Ctrl/Cmd+Shift+Z redo check is case-sensitive to lowercase z

- **Severity:** Low. **Confidence:** High.
- **Evidence:** `webview-ui/src/hooks/useEditorKeyboard.ts:71–79`, specifically the redo branch checks `e.key === 'z'` while also requiring `e.shiftKey`.
- **Technical explanation:** standard KeyboardEvent `key` reports uppercase `"Z"` when Shift is held for the Z key. The Shift+Z redo branch therefore does not match that key value; Ctrl/Cmd+Y remains a separate redo path.
- **Impact:** the conventional Ctrl+Shift+Z / Cmd+Shift+Z redo shortcut may not work; the alternative Ctrl/Cmd+Y path remains available where supported.
- **Verification:** `SOURCE-VERIFIED` / `STATIC-FINDING`; behavior not runtime reproduced.
- **Remaining uncertainty:** browser/OS keyboard mapping and platform shortcut conventions should be confirmed in the target host before prioritizing a change.

### Execution limits

Static source and documentation review only. No tests, build, lint, typecheck, browser/VS Code runtime, keyboard simulation, or application-source modification was performed.

### Next continuation point

Continue the remaining **16 reported-review source rows**: 9 files under `webview-ui/src/characters/ui/` and 7 other source files. Then proceed with the 47 inventoried-only tests, protocol producer-consumer tracing, host lifecycle/reconnect, and outstanding binary/font evidence. The `components/` (54/54) and `hooks/` (24/24) source scopes are complete at this source baseline.


## 14. Character subsystem direct review — GOV-WEBVIEW-007 (2026-11-25)

**Source baseline:** `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Scope:** complete `webview-ui/src/characters/` subtree.

### Inventory and SHA reconciliation

- Complete tree contains **115 files** under `src/characters/`.
- FILE-REFERENCE contains exactly 115 character rows: 0 missing paths, 0 extra paths and 0 blob SHA mismatches.
- Breakdown: **33 source files** (TypeScript/TSX/CSS), **81 PNG art assets**, and **1 wardrobe JSON manifest**.
- Historical status before this session: 23 source files REVIEWED, 1 source file with prior body review and EOF boundary evidence, 9 source files REVIEWED REPORTED, 81 PNGs BINARY HEADER VERIFIED, and wardrobe.json REVIEWED.
- The 9 REVIEWED REPORTED rows were individually fetched in full and statically reviewed. They are now REVIEWED. All 33 source files have review evidence in the ledger; the previously reviewed source files were not re-reviewed for the same question.
- All 81 PNGs retain their historical PNG signature and 128×96 IHDR evidence. Full CRC validation, pixel decode and visual inspection were not performed in this session.
- `art/wardrobe.json` retains its historical parsed/manifest-consistency evidence. It was not redundantly reparsed.
- The subtree is fully accounted for; no character file was omitted from path/SHA reconciliation.

### Newly reviewed source files

| File | Blob SHA | Lines |
|---|---|---:|
| `webview-ui/src/characters/ui/ArmedButton.tsx` | `b773162e20ae6aa3620bfb4ae0da08e8fb00981c` | 40 |
| `webview-ui/src/characters/ui/characters.css` | `b299ebb659f58b4b253214c18761c8d1d18c081e` | 928 |
| `webview-ui/src/characters/ui/characters.phone.css` | `269248f4433198dee264e72ddc5896cd4611ff59` | 373 |
| `webview-ui/src/characters/ui/ColorPanel.tsx` | `c944c2c7aa40d00021f4689763e15472bb8fd6e6` | 376 |
| `webview-ui/src/characters/ui/ItemTools.tsx` | `68cc48ad0097c7b4ee3096fa02f7447c0d465cb8` | 151 |
| `webview-ui/src/characters/ui/SaveButton.tsx` | `d96773f83779aecc12d3ba64ea43f6fbcdf84ea8` | 24 |
| `webview-ui/src/characters/ui/studio.ts` | `815583783ab3c2150230bce1c20934177832f36b` | 79 |
| `webview-ui/src/characters/ui/ViewButton.tsx` | `9c22f04ee2deb6489666128247122b5588201b7b` | 34 |
| `webview-ui/src/characters/ui/Wardrobe.tsx` | `5fb4e456225f297b23400e26363ae84fe09714a0` | 275 |

### Cross-reference and lifecycle observations

- `CharactersView.tsx:48–70` owns status/toast timeout and pointerdown dismissal; `:92–108` brackets undo steps with root pointerdown and window pointerup/pointercancel and removes all three listeners on cleanup.
- `CharactersView.tsx:218–250` installs a global keyboard handler with `isTextEntry` guard; editable inputs, textareas and contenteditable elements are excluded, while range inputs remain eligible for view shortcuts. This is a useful contrast with the editor keyboard handler recorded in `WV-C15`.
- `Stage.tsx:87–124` runs the preview animation through requestAnimationFrame and cancels the last scheduled frame on cleanup.
- `Roster.tsx:163–191` removes the ResizeObserver and the document pointerdown listener when the phone dock / More sheet effects end.
- `PixelEditor.tsx:260–354` maps pointer coordinates to sprite cells, captures the active pointer, paints Bresenham-interpolated cells and clears stroke state on pointerup/pointercancel.
- `ItemTools.tsx:43–69` catches PNG decode failures and revokes the temporary object URL in `finally`; `loadWardrobe.ts:48–57` retains the previously recorded aggregate wardrobe-load rejection risk (`WV-C11` / `WV-WARD-01`), not a new finding.
- `castStore.ts:28–55` decodes custom item PNGs and caches decode results; `decodeItemPng` catches decode failures and returns null. The cast save operation is synchronous at the UI boundary (`cast.ts:356–378`), records the in-flight save and sends the saved snapshot; the close-confirmation save/discard sequence in `CharactersView.tsx` therefore does not await an asynchronous return from `castStore.save()`.
- The two character CSS files were reviewed as complete style sheets, including desktop/phone layouts, dock, wardrobe, picker, pixel editor, roster, focus-visible treatment and reduced-motion rules.

### Findings

No new stable-ID defect finding was added from this pass. The review confirms UI lifecycle cleanup paths and identifies no new issue strong enough to promote beyond an observation. Existing character-related findings `WV-C09`, `WV-C10`, `WV-C11` and `WV-WARD-01` remain unchanged and OPEN as recorded in the existing deep-audit sections.

### Verification limits

- Static source and ledger review only.
- No tests, build, lint, typecheck, browser/VS Code runtime, accessibility audit, pointer simulation, image decode, PNG CRC check or visual regression was executed.
- No application source, test, configuration, dependency or asset file was modified.

### Next continuation point

The 9 character UI reported-review rows are closed at the source-review evidence level. **7 reported-review source rows remain elsewhere in `webview-ui/src/`**, in addition to 7 `FETCHED / REVIEWED REPORTED` rows requiring their own coverage reconciliation, 47 inventoried-only tests, 143 binary-header-only assets and 1 unverified font. Continue with the 7 other source rows; do not re-review the 115 character subtree files without changed SHAs or a new cross-module question.


## 15. Remaining webview source coverage — GOV-WEBVIEW-008 (2026-11-25)

**Source baseline:** `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Scope:** the seven remaining `FETCHED / REVIEWED REPORTED` source rows in `webview-ui/src/`.

### Governance and inventory reconciliation

- Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, DEEP-AUDIT and FILE-REFERENCE before source inspection.
- Reconciled the complete webview subtree against FILE-REFERENCE: **459/459 blobs**, 0 missing paths, 0 extra paths and 0 SHA mismatches.
- The seven rows labelled `FETCHED / REVIEWED REPORTED` were the same seven remaining source rows referred to by the prior continuation note; they were not two separate groups. The earlier wording implying two independent groups is corrected by this session.
- All seven files were individually re-fetched in full at the source baseline. Their current blob SHAs match the ledger; each complete file was statically reviewed and its line coverage is now recorded.

### Files directly reviewed

| File | Blob SHA | Lines |
|---|---|---:|
| `webview-ui/src/changelogData.ts` | `a7a9c67afa47a0fdab0c63de91a25e9ead3389be` | 1–341 |
| `webview-ui/src/constants.ts` | `9580f95a5239485b5cb83a6ebe6c3b79010a19cc` | 1–880 |
| `webview-ui/src/index.css` | `daa1fcad26dbb03a9fa4ef0e1cf16389b7577b33` | 1–697 |
| `webview-ui/src/loadingPreview.tsx` | `9cd3169706acf23a2b3ea4a7d92fdc8b41fc855b` | 1–14 |
| `webview-ui/src/notificationSound.ts` | `f30c00d2463e79724e7d57ad56420d92b2625211` | 1–128 |
| `webview-ui/src/runtime.ts` | `39316cd1563dc344232832f90fb23c82dff4c0c4` | 1–28 |
| `webview-ui/src/testHooks.ts` | `e2f7fe210be54bd6448c7735b0baaf29dfe9ca32` | 1–352 |

### Cross-reference observations

- `App.tsx:54–66` imports and installs `installTestHooks` only when `isE2E` is true. The test hook namespace and `OfficeState.prototype.addAgent` wrapper in `testHooks.ts:139–140, 324–352` are therefore gated by the E2E harness at the observed application entry point.
- `ChangelogModal.tsx:1–6, 44–48` consumes `changelogEntries`, `toMajorMinor` and the repository/Discord URLs from `changelogData.ts`; the contributor links use `target="_blank"` with `rel="noopener noreferrer"`.
- `main.tsx:1` and `loadingPreview.tsx:2` both import the shared `index.css`; `loading.html:12` mounts the dev-only loading preview. `LoadingScene.tsx` consumes the loading constants and `loadLoadingArt()`.
- `useExtensionMessages.ts:8, 634, 647` consumes `playDoneSound` and `playPermissionSound`; both functions guard unavailable audio and catch failures around context creation/resume/playback.
- `runtime.ts` uses a `typeof` check for the optional `acquireVsCodeApi` global and gates E2E diagnostics on the preloaded `__PIXEL_AGENTS_E2E` flag. These paths were inspected statically only.

### WV-C17 — Reduced-motion preference does not suppress two continuous UI animations

- **Severity:** Low. **Confidence:** High.
- **Status:** SOURCE-VERIFIED / STATIC-FINDING / OPEN; no browser accessibility or motion-preference runtime check performed.
- **Affected files:** `webview-ui/src/index.css`, with consumers in `ConnectionIndicator.tsx`, `DebugView.tsx`, `ToolOverlay.tsx` and `usage/PixelHeart.tsx`.
- **Evidence:** `index.css:303–306` assigns an infinite `pixel-pulse` animation; its keyframes are at `:404–410`. The reduced-motion blocks at `:432–435` and `:635–646` disable `pixel-pop` and the listed menu/modal animations, but do not disable `.pixel-pulse`. Separately, `:656–670` defines the usage-heart animation and `:692–696` applies it infinitely; neither reduced-motion block disables `.usage-heart-beat`. Consumers include `ConnectionIndicator.tsx:34`, `DebugView.tsx:39`, `ToolOverlay.tsx:313`, and `PixelHeart.tsx:59–74`.
- **Technical explanation:** when the operating system/browser advertises `prefers-reduced-motion: reduce`, these two CSS animation paths remain active because the media-query overrides do not include their classes or animation names.
- **Impact:** users who request reduced motion may continue to see pulsing connection/activity indicators and the repeating usage-heart animation.
- **Remaining uncertainty:** no target-browser verification was run; product intent for these two status/feedback animations has not been documented in the inspected source. This is an accessibility concern, not a claim of a runtime failure.

### Review result and execution limits

- The seven remaining reported-review source rows are now `REVIEWED`; the ledger contains no remaining `FETCHED / REVIEWED REPORTED` or `REVIEWED REPORTED` rows.
- No additional finding was warranted for changelog data, constants, loading preview, sound dispatch, runtime detection or E2E hooks.
- No tests, build, lint, typecheck, browser/VS Code runtime, accessibility audit or audio experiment was executed.
- No application source, test, configuration, dependency or asset file was modified.

### Next continuation point

Continue with the **47 `FETCHED / INVENTORIED` test files**, inspecting complete assertions and mapping them to implementation without executing them. Then continue ClientMessage/ServerMessage producer-consumer tracing, host lifecycle/reconnect, and the outstanding 143 binary-header-only assets plus the unverified font. Preserve all existing `WV-` findings and keep runtime verification open.


## 16. Assertion-level review of remaining webview tests — GOV-WEBVIEW-009 (2026-11-25)

**Source branch:** `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, recursive `truncated: false`  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction` at `fd998227336bb3f196d0a26caeea72dc96d7a9a0`  
**Scope:** all 47 webview test files previously classified `FETCHED / INVENTORIED`; complete test source fetched and assertions reviewed.  
**Explicit exclusions:** no test execution, build, lint, typecheck, browser/VS Code runtime, source modification or test modification.

### Coverage reconciliation and result

- Re-read current governance and webview audit ownership documents before investigation.
- Reconciled the 47 ledger entries against the active source tree and individually re-fetched all 47 test files at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- All 47 current blob SHAs matched the ledger; no missing, changed or untracked test path was found.
- Complete assertions and helper setup were inspected. The tests were mapped to the implementation modules they import; coverage themes and limitations are summarized below.
- All 47 ledger rows are promoted to `TEST-INVENTORIED`. Together with the 7 already in that status, all 54 tracked webview test files now have assertion-level inventory/review evidence. **None has been executed in this session.**

### Per-file assertion map

The blob SHA for every row is recorded in [FILE-REFERENCE.md](FILE-REFERENCE.md); this table records the test intent and assertion scope without duplicating the ledger.

| Test file | Blob SHA | Assertion scope / implementation area |
|---|---|---|
| `webview-ui/test/cardBar.test.ts` | `39e3b000b1aef67abd7f0f342330af44437c0944` | Card variant precedence, eligible card order, close affordance and pointer reorder. |
| `webview-ui/test/castHistory.test.ts` | `3de1c964733ca76f8472906d15572f1d4313ef85` | History grouping, explicit steps, undo/redo frame state, step limit and clear. |
| `webview-ui/test/characterSheet.test.ts` | `8e3c4de80cedafb49046afd15a6c578b1a51a04b` | Current/legacy PNG decode, sprite slicing, legacy widening and invalid dimensions. |
| `webview-ui/test/characterTemplatesVersion.test.ts` | `b34861e202e317340a3e6402f03602f4a6c8b8e0` | Template replacement notification/version, sprite lookup and unsubscribe. |
| `webview-ui/test/clipboardKeys.test.ts` | `6fd711c2e492c7fa7695104c90ce18952e47373a` | Platform/modifier clipboard routing, selection-aware copy, copy-as-text and OSC 52 validation. |
| `webview-ui/test/contextGauge.test.ts` | `a4b3cc4c4a96f3169afde059e3128af6df5924a1` | Threshold colors, remaining-share clamping, invalid inputs and OfficeState zero-context propagation. |
| `webview-ui/test/conversationsRecent.test.ts` | `56e1c4a7d27821aec41b4c7c780217c0825f88d7` | Cross-directory recent ordering, limit and directory association. |
| `webview-ui/test/despawnSeat.test.ts` | `f9354dd17f64bde82d6990f9f77cfa75350f0557` | Seat release across despawn/layout rebuild, reassignment and helper seat exclusion. |
| `webview-ui/test/directoryStrip.test.ts` | `fdc1cc3089fc2eade4f468ee86c6c3393f570045` | Default/priority/random directory ordering, overflow silhouette and deleted-character filtering. |
| `webview-ui/test/edgeSwipe.test.ts` | `01da285159b5c81c02a374dd02a5acc3658afa7e` | Edge arming, opt-out, native blocking, drag/commit thresholds, cancellation and touch identity. |
| `webview-ui/test/editorActions.test.ts` | `b27ca7b3529a019a542fc0bc701d0672da662fff` | Furniture-group rotation, rider collision validation and grid bounds. |
| `webview-ui/test/editorState.test.ts` | `e2122f1b8c0da2e0390aef840a518602e0235ab0` | Copied placement color precedence, absent color preservation and palette handoff. |
| `webview-ui/test/flowCopy.test.ts` | `4d8c35241b72d6516dd3c101288b577631a6b017` | Terminal copy reflow, structural/indent preservation, broken URLs and selection offsets. |
| `webview-ui/test/greeter.test.ts` | `116ae19c1446e888eb763214d13249f48dec357f` | Greeter spawn/despawn, idempotence, camera, non-agent/persistence and render invariants. |
| `webview-ui/test/greeterCharacter.test.ts` | `b164d53b18a95d88198afbcca6db780084312b2a` | Greeter profile identity through rename/reorder and fallback. |
| `webview-ui/test/helperFollow.test.ts` | `f952914e490d06cbf12331b3f98465e41d4b2064` | Helper following, lead seat anchor, work pose, spacing and stand-up delay. |
| `webview-ui/test/inputArea.test.ts` | `af99bc832e212079e6865c1c20b3924ac5b7f83d` | Prompt input-area row detection and rule recognition. |
| `webview-ui/test/introBubbleGeometry.test.ts` | `bd969a0f7869f787f2a3833be47eec3bf08632cf` | Bubble anchor/clamp, tail geometry, unmeasured state and camera caps. |
| `webview-ui/test/introTour.test.ts` | `86fab0a9080ba5336818bcd014b81d18f2505642` | Consent reducer races, provider correlation, install verdict, revised choice and reset. |
| `webview-ui/test/keyboardViewport.test.ts` | `c84215e44d8a718b1ea82cef06186777870b3bed` | Keyboard detection across viewport resize models, focus, orientation and dismissal. |
| `webview-ui/test/launchLatch.test.ts` | `b4eda6652c842ddee7e68a75f68469dd490d2e93` | Launch terminal response latch and timeout boundaries. |
| `webview-ui/test/launchMenus.test.ts` | `958bcd3ea604559a65e47b5f3b9acd0762fee037` | Drawer/roster state transitions, pick retention and invalidation. |
| `webview-ui/test/layoutSerializer.test.ts` | `bdc28518315db12c7c0981701baff3d52ff67a89` | Pets migration, tileColors regeneration, legacy void migration and idempotence. |
| `webview-ui/test/musicComposer.test.ts` | `ad5eb57cda316b2bc0dd5f99a332840e5246f8b4` | Seeded song output, song-code roundtrip/fingerprints, duration/monophony and mood distribution. |
| `webview-ui/test/officeBlink.test.ts` | `ad40215944dbb2e5bec43e638130cfc28d5f040c` | Blink gap/focus/duration irregularity and inter-character desynchronization assertion. |
| `webview-ui/test/officeBlinkSprites.test.ts` | `6b8b14673e493221060913b8d04f2cb992debfc5` | Blink sprite differences, repeat sprite variants and permanently closed eyes. |
| `webview-ui/test/officeCanvasCursor.test.ts` | `5bde5502619ceede4d3e6603a47885eada49151a` | Cursor priority for character/pet hit, seat reassignment and default. |
| `webview-ui/test/petEntity.test.ts` | `0897047475e5b1cf3d1595b669d66962b983a34c` | Pet idle/walk/follow FSM, target loss, timing, animation and defensive cases. |
| `webview-ui/test/pieceItems.test.ts` | `31a96bd13dee646e00dd70346f4679b58f8f53e1` | Sprite-sheet per-cell edit extraction for additions, recolor, erase and no-op. |
| `webview-ui/test/pixelFont.test.ts` | `18531e885497d230b876dd6cf4bdc72ac1c5b158` | Glyph label segmentation/geometry, month glyph coverage and timeAgo. |
| `webview-ui/test/pixelGrid.test.ts` | `9ad8c421ba795f37e127eb8f8d276ecd57aabc09` | Grid/color run aggregation, offsets, clear-cell and lit bounds. |
| `webview-ui/test/pixelSnap.test.ts` | `e6e9e8a165b37c379832ebfecbb0473f5b6cdf33` | Device-pixel rounding, minimum size and float precision. |
| `webview-ui/test/playerFit.test.ts` | `ffafbaecc2feac7a724bd171bb8d86f604beba9e` | Music player responsive fit and terminal drawer width bounds. |
| `webview-ui/test/playerSheet.test.ts` | `2142af5f2536c21bf3bfce214bd10b6454471cbf` | Player sheet touch slop, axis lock, clamping, flick and settle. |
| `webview-ui/test/recentCharacters.test.ts` | `061396d8d16006d6fae1a4e8f045539babd657dd` | Roster recency order and duplicate/out-of-range filtering. |
| `webview-ui/test/seatedIdle.test.ts` | `a82c8f9bb099e7ba81dc1abcd8482043658d844d` | Agent/helper seated sprite state and restored/replayed activity. |
| `webview-ui/test/selectionButtons.test.ts` | `b58db130650c087b139bc4e833f594bfc78edae6` | Selection button hit geometry, placement and edge bounds. |
| `webview-ui/test/selectionMath.test.ts` | `110632affff46ab5a788e99770f191b83a9b7b86` | Word/range selection, anchor extension, handle clamping, cell mapping and pill geometry. |
| `webview-ui/test/slideTransitions.test.ts` | `43beb2021446681e7d2954107aef3da3a3cf319d` | Panel/content transition timing and reduced-motion instant behavior. |
| `webview-ui/test/teammateSeating.test.ts` | `4f84bd6b7ebea60bdad715d0ee9842198fca90c0` | Lead seat anchor vs live tile and seatless fallback. |
| `webview-ui/test/terminalLinks.test.ts` | `9454e202fc3d1a8c781824faac6769c7993b2a20` | HTTP(S) opener allowlist, wrapped xterm URL/cell detection and regex behavior. |
| `webview-ui/test/touchGesture.test.ts` | `fce4e63d43ace5db03782479e4a7980f3f0ca94f` | Touch reducer multi-touch, long-press, tap/URL, scroll/flick, handles and paste. |
| `webview-ui/test/trackpad.test.ts` | `8bf930c4a4bd3545c634fc62b0579bd60c7c7bb2` | Trackpad axis lock, pause-based axis switching, accumulation and edge acceleration. |
| `webview-ui/test/usageBarView.test.ts` | `6259843f93d04290a98b705a2093caa5c10065ea` | Usage percentage/reset/freshness, duration formatting and heartbeat period. |
| `webview-ui/test/usageStore.test.ts` | `301c057bd160e6b02762a940c0ed6d54da3b5677` | Provider status arrival sequence, malformed top-level input, limits and pending response. |
| `webview-ui/test/wardrobeLabels.test.ts` | `756676f90fa1195937dd4dd7db6fcbdf041481ca` | Wardrobe part labels/tab summary and hue normalization. |
| `webview-ui/test/workstations.test.ts` | `331448e5cdc5ce20f27f682cc433e19c80f417c7` | Chair-PC pairing, orientation/uniqueness and active-seat PC state. |

### WV-TEST-01 — Blink desynchronization test does not simulate concurrent independent clocks

- **Severity:** Low. **Confidence:** High.
- **State:** OPEN — TEST-INVENTORIED / STATIC TEST-FINDING; no test execution or runtime experiment.
- **Affected test:** `webview-ui/test/officeBlink.test.ts`.
- **Related implementation:** `webview-ui/src/office/engine/blink.ts`.
- **Evidence:** test helper `run()` creates and advances one blinker using the supplied random function (`officeBlink.test.ts:25–38`). The test named “two characters started together blink out of step” creates one RNG at line 72, then runs the entire first 30-second simulation before running the second 30-second simulation with the same already-advanced RNG at lines 73–75. The implementation creates a separate clock in `startBlink()` and advances each clock in `tickBlink()` (`blink.ts:29–50`).
- **Technical explanation:** the assertion compares two sequential simulations consuming disjoint portions of one PRNG stream. It does not instantiate two blinkers at the same start time and advance them interleaved. Therefore, it can pass because the second simulation receives later random draws, without proving the stated concurrent desynchronization invariant.
- **Impact:** regression protection for “independent character clocks stay out of step” is weaker than the test name and implementation comment imply. This is a test-quality gap, not evidence that production blink behavior is defective.
- **Suggested verification shape (not implemented):** create two blinker states at the same logical start, give them controlled distinct random streams (or deterministic interleaved draws), and tick both on each frame before comparing their blink timelines.
- **Remaining uncertainty:** no tests were run; this finding assesses test construction statically only.

### Cross-test coverage gaps and existing finding correlation

1. The majority of this test set exercises pure functions, reducers and domain-state methods. It does not establish component DOM behavior, browser event wiring, VS Code postMessage behavior, WebSocket dispatch, host persistence or actual mobile Safari gesture behavior.
2. `touchGesture.test.ts` and `edgeSwipe.test.ts` exercise reducers with synthetic events; they do not verify browser listener registration/cancellation semantics or real device gesture arbitration. Preserve existing `WV-C12` and lifecycle questions; do not infer runtime reproduction.
3. `usageStore.test.ts` checks malformed top-level identifiers and status sequencing, but does not comprehensively assert malformed nested `usageLimits` payload shapes. This aligns with the existing validation gap `WV-C08` / server `F-08`; no duplicate finding is created.
4. `terminalLinks.test.ts` gives useful scheme-allowlist and xterm-buffer coverage, but the tests use a mocked opener; they do not verify host/browser external-link policy or actual navigation.
5. `characterSheet.test.ts`, `officeBlinkSprites.test.ts` and `wardrobeLabels.test.ts` load real repository assets/manifests in their test setup. Their assertions are inspected, but the PNG decoding and fixture availability paths remain unexecuted in this audit.
6. `musicComposer.test.ts` includes deterministic seeded song fingerprints but also a probabilistic mood-distribution assertion using repeated `Math.random` calls; its execution stability and distribution thresholds remain unverified.
7. The test suite does not close the existing application-level reconnect, transport subscriber exception, dirty-layout snapshot, runtime schema-validation or host lifecycle findings. Those remain in their owning records.

### Updated coverage status

- `webview-ui/test/`: 54/54 files are now `TEST-INVENTORIED` (7 previously inventoried + 47 reviewed in this session).
- `FETCHED / INVENTORIED`: 0 remaining.
- `REVIEWED`: 261; binary header only: 143; not verified: 1 font.
- All 459 webview paths remain SHA-reconciled at the active source snapshot.
- No existing finding was closed. One test-quality finding, `WV-TEST-01`, was added.

### Verification limits

- `TEST-INVENTORIED` means assertions and setup were read and mapped; it does not mean `TEST-EXECUTED`.
- No npm/vitest command, build, lint, typecheck, browser, VS Code extension host, accessibility or mobile-device experiment was run.
- No source or test file was modified.

### Next continuation point

1. Continue the ClientMessage/ServerMessage producer-consumer trace through UI, adapter and server, focusing on field-level validation, authorization, persistence, side effects and handshake ordering. Reuse `WV-C08`, `WV-APP-01`, `WV-RESYNC-01` and `WV-TR-01`; do not duplicate them.
2. Continue targeted lifecycle/disposal/reconnect analysis across webview, adapter and server.
3. Retrieve the unverified font binary internals when available; preserve the 143 assets as header/signature-level evidence only.
4. Test execution remains unauthorized and open; request explicit authorization before running tests or other dynamic checks.


## 17. Control-plane producer-consumer trace — GOV-WEBVIEW-010 (2026-11-25)

**Source branch:** main at 56f354f2808ae5aba10b0e1d5892bd9fa7ca095b  
**Source tree:** 2d02bb29bd5a3aa8f74fc6a2ed21027149817e50; 856 entries, 739 blobs, truncated=false  
**Documentation branch before publication:** docs/hybrid-llm-engine-direction at 189e28970ec5d05abd536a57a4adab53399c27fd  
**Scope:** complete static producer → transport → host ingress trace for all 34 ClientMessage variants, and producer → delivery/fan-out → UI consumer trace for all 46 ServerMessage variants.  
**Governance read before source inspection:** governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview/server/adapter module records, webview DEEP-AUDIT and FILE-REFERENCE, PROTOCOL-MATRIX, TRANSPORT and STATE-MANAGEMENT.

### Repository and coverage reconciliation

- Reconfirmed source branch main at 56f354f2808ae5aba10b0e1d5892bd9fa7ca095b and recursive tree 2d02bb29bd5a3aa8f74fc6a2ed21027149817e50 (truncated=false).
- Reconfirmed docs branch before publication at 189e28970ec5d05abd536a57a4adab53399c27fd.
- The webview FILE-REFERENCE rows for the re-fetched UI producer/consumer files match their current blob SHAs; all inspected files were already classified REVIEWED. No source ledger status needed promotion.
- Server and adapter modules remain at the previously reconciled source baseline: 135/135 server paths and 8/8 adapter paths. Relevant source files were selectively re-fetched for this cross-layer question; this did not restart either module audit.
- AsyncAPI remains the source of truth: 34 ClientMessage variants and 46 ServerMessage variants. The generated core/src/messages.ts remains generated and was not edited.

### Producer-consumer coverage result

- **ClientMessage:** 34/34 variants mapped from UI producer to transport and host ingress or documented local/browser behavior.
- **ServerMessage:** 46/46 variants mapped to producer family, delivery scope (store broadcast, direct reply, handshake snapshot, watcher or VS Code-only event) and UI consumer.
- Five ClientMessage variants have no explicit standalone switch case: focusAgent, exportLayout, importLayout, exportFile and openSessionsFolder. Four are VS Code-only host actions with browser/local alternatives; focusAgent is emitted in both runtimes, but standalone intentionally no-ops the server side while App handles local drawer/tab focus.
- Conversation requests remain standalone-only because the UI is gated on terminal availability; the VS Code adapter does not implement requestConversations, searchConversations or resumeConversation.
- Raw terminal output/replay/input remains a separate WebSocket data plane and is not counted among the 80 control-plane variants.
- Full producer, ingress, consumer and recipient map is in PROTOCOL-MATRIX.md sections 9–11.

### Key cross-layer facts

1. The standalone WebSocket boundary parses JSON and casts to Record<string, unknown>; it does not validate the ClientMessage union at ingress. The server's privileged gate is fail-closed except for webviewReady. The VS Code adapter trusts its own webview boundary but still dispatches runtime message objects without a comprehensive schema validator. This is the existing WV-C08 / F-08 concern, not a new validation finding.
2. ServerMessage delivery is not uniform: store broadcasts reach all connected standalone sockets and the VS Code webview listener; handshake snapshots, diagnostics, conversation results, directory acknowledgements and some hook/asset responses are point-to-point.
3. areaMappingsLoaded is sent during handshake; saveAreaMappings persists config but does not publish a new snapshot to other active clients. OfficeState uses that mapping when placing a new agent, while agentCreated is delivered to all connected clients. This creates a conditional multi-client state divergence risk.
4. External asset directory mutations update shared config/cache but the reload messages are sent using the requesting socket's send callback. Other already-connected standalone clients do not receive the refreshed list/sprites until a later handshake/reconnect.
5. Some other host-global mutations are also point-to-point or persist-only (for example hooksStatus after setHooksEnabled and settings affected by setWatchAllSessions). Their intended multi-client consistency contract remains an OPEN question; they are recorded in the matrix rather than promoted to another finding in this session.
6. saveDirectory and removeDirectory share one handler across hosts. Successful mutations broadcast directoriesLoaded; save additionally acknowledges the sender with directorySaved; rejection and suggestions are point-to-point.
7. saveCharacterProfiles uses shared parsing/persistence and broadcasts successful profile snapshots; refused saves reply only to the sender. saveCardOrder parses and filters against existing agent IDs, then broadcasts the accepted order.
8. Usage snapshots are sent only to privileged clients; httpServer also filters usageBridgeStatus and usageLimits broadcasts for read-only sockets.

### Source identity of the selectively re-fetched trace files

| File | Current blob SHA |
|---|---|
| core/asyncapi.yaml | ff1247118819fe68852da11996c660df1726aa66 |
| webview-ui/src/App.tsx | 3eb91a2050da5f789223bc0a045a80fc841d60cb |
| webview-ui/src/hooks/useExtensionMessages.ts | 649025df90edef84d24c40556cee2ea007e15ccf |
| webview-ui/src/hooks/useDirectoryEditor.ts | abf44c4dfc59fd57cdcc0193b31385a5e7bcebad |
| webview-ui/src/hooks/useLaunchActions.ts | 88d01bb2f03bade7f4f8030e25a34e90687bfbca |
| webview-ui/src/hooks/useEditorActions.ts | 386b839a124ffad437c8a3cd9c3460e2826c0d00 |
| webview-ui/src/components/SettingsModal.tsx | ca9248f01ba8631a81b0489fcb152203f3b553d3 |
| webview-ui/src/characters/castStore.ts | 5f3186567cd783f727b487543b4e9a1157e0749c |
| webview-ui/src/characters/ui/files.ts | e9842275fee39cc3709453329b74a5f22d809281 |
| webview-ui/src/hooks/useIntroTour.ts | 4fc18a2eef5bb2b73bcf6a4bf5facfc17d25a28d |
| webview-ui/src/usage/useUsageBridge.ts | 270909fcba42e615b95d6229b66d61b1224f9072 |
| webview-ui/src/conversations/useConversations.ts | e360d9eb264502582c7a07f205fb0fc82910c8a3 |
| webview-ui/src/conversations/ConversationSearch.tsx | da5eb5efdd143572270909be52e680ae62f56b2c |
| webview-ui/src/components/DebugView.tsx | ccc1f465d3777b65bfb79c870d5cb6eb3ef95c5e |
| webview-ui/src/office/engine/officeState.ts | 1afd778f4c72f1901ef91266e29a89f117e180c7 |
| adapters/vscode/PixelAgentsViewProvider.ts | 94d1ab2e1427377e67ff430a852d49863e5e4d87 |
| adapters/vscode/agentManager.ts | 30020b3e34d2d83fa4536e0d27f55c9e4fe3295c |
| server/src/clientMessageHandler.ts | d7ae8235ee75de63b1716a4b67f27d69023f5d96 |
| server/src/httpServer.ts | ad5104a14233aa29834e008833a0f21458fc887b |
| server/src/fileWatcher.ts | 4f409487b7593ee8f3ec5ce29cf7627f4a39a11f |
| server/src/hookEventHandler.ts | bd430b99f4abf7afea851645fe31924bf6213f43 |
| server/src/transcriptParser.ts | a706cb5549ae67fee2cbe3ec3420fda3bed0013a |
| server/src/timerManager.ts | c01dcfc964830289b9ee8e2329da444c06fbf1b7 |
| server/src/agentActivityResend.ts | f0c6fe190f9d259c6c172cfd7a4198ecc64dfb77 |
| server/src/contextUsage.ts | b77d0515bb5b806a8c2652ef4fab744bfec37757 |
| server/src/agentMessages.ts | df521f7a6d9ddbad8d7ee0c567df4054c500b9ea |
| server/src/directories.ts | a27617b843733ff8eb5a6a0263324587bb57a32b |
| server/src/characterProfilesPersistence.ts | 6444df2a8a032f0023426a226244d25cd7178a85 |
| server/src/cardOrder.ts | 3c4d7bd9397546dfe01081593045a9edaa57a0fa |
| server/src/layoutPersistence.ts | 02a65d6237eec3ff6e867d1ab0548c88173a2e2b |
| server/src/usageLimits.ts | b28ebe7b43d8df3179cfb44c4abcd3b980539f8a |
| server/src/cli.ts | 2ab60f30b17b856d17c68435337663aab000b7e7 |
| server/src/terminal/standaloneAgentLauncher.ts | f17562fcf673e5b8db4a7bd94d9ff953c4edf0b4 |
| server/src/terminal/terminalRoutes.ts | 434ac1db106d593577409b42e872331ac54e4323 |

### WV-PROTO-01 — Area mapping mutation is not propagated to other connected clients

- **Severity:** Medium. **Confidence:** High.
- **Affected files:** webview-ui/src/App.tsx; webview-ui/src/hooks/useDirectoryEditor.ts; webview-ui/src/hooks/useExtensionMessages.ts; webview-ui/src/office/engine/officeState.ts; server/src/clientMessageHandler.ts; adapters/vscode/PixelAgentsViewProvider.ts; server/src/httpServer.ts.
- **Evidence:** App.tsx:282–303 and useDirectoryEditor.ts:64–78 update the sender's OfficeState and send saveAreaMappings. Standalone clientMessageHandler.ts:436–444 writes config.standalone.areaMappings but emits no store broadcast or areaMappingsLoaded reply. VS Code PixelAgentsViewProvider.ts:635–639 writes config.vscode.areaMappings without sending a new snapshot. useExtensionMessages.ts:779–783 only updates local state on areaMappingsLoaded, and the standalone snapshot is sent at clientMessageHandler.ts:696–701 during webviewReady. OfficeState.findFreeSeat reads the local mapping at officeState.ts:368–395. The same server process fans agentAdded to every socket at httpServer.ts:263–280.
- **Technical explanation:** a second connected standalone client retains the previous mapping snapshot. A subsequent shared agentCreated causes each UI instance to select a seat using its own local areaMappings.
- **Impact:** under multiple connected clients, one shared agent may appear in different seats in each office rendering.
- **Verification:** SOURCE-VERIFIED / STATIC-FINDING / OPEN; not runtime reproduced.
- **Remaining uncertainty:** expected cross-client seat/layout consistency has not been validated in a live multi-client session; VS Code's normal single-webview mode reduces that host's exposure.

### WV-PROTO-02 — External asset directory reload is delivered only to the requesting client

- **Severity:** Low. **Confidence:** High.
- **Affected files:** webview-ui/src/components/SettingsModal.tsx; webview-ui/src/hooks/useExtensionMessages.ts; server/src/clientMessageHandler.ts; server/src/cli.ts; adapters/vscode/PixelAgentsViewProvider.ts.
- **Evidence:** clientMessageHandler.ts:405–425 sends externalAssetDirectoriesUpdated via the requesting socket's send callback and calls onReloadAssets(send). cli.ts:382–415 mutates the shared assetCache but sends refreshed character, pet and furniture assets through that same callback. useExtensionMessages.ts:872–875 updates only the receiving UI. The VS Code adapter posts the updated list/assets directly to its one webview at PixelAgentsViewProvider.ts:1008–1041.
- **Technical explanation:** other connected standalone clients receive neither the new externalAssetDirectories list nor the refreshed sprites. A later handshake receives the mutated shared cache, but an already-connected client can retain stale UI asset state until reconnect/reload.
- **Impact:** concurrently open standalone clients can display different character/pet/furniture catalogs after one client changes external asset directories.
- **Verification:** SOURCE-VERIFIED / STATIC-FINDING / OPEN; not runtime reproduced.
- **Remaining uncertainty:** intended immediate asset synchronization semantics for multiple clients are not documented.

### Related existing findings — preserved, not duplicated

- WV-C08 and server F-08 remain the canonical runtime-schema validation gap; this trace supplies per-variant examples but does not create a duplicate schema finding.
- WV-RESYNC-01 and WV-APP-01 remain the canonical reconnect/layout snapshot concerns.
- WV-TR-01 remains the canonical WebSocket subscriber-exception dispatch concern.
- Adapter A-01–A-10 and server F-01–F-08 remain unchanged.

### Verification limits and next continuation

- Static source and protocol documentation only; all 80 control-plane variant paths are inventoried, not runtime exercised.
- No tests, build, lint, typecheck, protocol generation, browser/VS Code host, multi-client WebSocket, PTY or reconnect experiment was executed.
- No application source, tests, assets, dependencies or configuration were modified.
- Next: confirm the intended multi-client state contract for WV-PROTO-01/02; complete per-field validation and authorization review without duplicating WV-C08/F-08; then proceed to targeted lifecycle/reconnect work when authorized.


## 18. GOV-WEBVIEW-011 — Field-level validation, authorization, persistence and side effects

**Scope:** all 34 ClientMessage variants, using the existing 34/34 producer-consumer trace from GOV-WEBVIEW-010. The detailed field-by-field matrix is in [PROTOCOL-MATRIX.md](PROTOCOL-MATRIX.md) §14–16.  
**Source ref:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. Static source retrieval only; no runtime or test execution.

### Coverage completed

- Compared AsyncAPI field requirements/types against the actual standalone WebSocket ingress, `handleClientMessage`, shared persistence helpers, and VS Code webview dispatcher.
- Classified each message for: field shape, runtime validation, read-only/privilege boundary, durable/runtime side effects, success/failure acknowledgement, and broadcast vs requester-only delivery.
- Confirmed that AsyncAPI is a declarative contract only: standalone parses JSON then casts to a record; VS Code dispatches the raw message object. Neither host applies a complete ClientMessage runtime schema validator.
- Confirmed the standalone connection-level gate refuses all non-`webviewReady` messages for unprivileged clients. This is fail-closed authorization but does not validate fields or provide per-command capabilities.
- Confirmed stronger shared validation for profiles/wardrobe, Directories, card order, conversation queries/session UUID and consent choices; partial or absent validation for seats, layout, area mappings, most settings, hook enabled boolean and external asset paths.
- Confirmed durable write boundaries and missing/optimistic acknowledgements. External asset directory handlers ignore `writeConfig` failure; profile + wardrobe files are atomic individually, not transactionally together.

### Findings

- Existing `WV-C08` / server `F-08` remains the canonical runtime-validation finding; this session adds the exact message-level cases listed above. No duplicate schema finding was created.
- `WV-PROTO-03` added: external asset directory add/remove can report the in-memory candidate as updated even when `writeConfig` returns false. Low severity, high confidence, OPEN, static only.
- Existing `WV-PROTO-01` (area mapping fan-out) and `WV-PROTO-02` (requester-only external asset reload) remain separate and OPEN.

### Evidence and limitations

Selected source blobs fetched from `main`: `core/asyncapi.yaml` ff1247118819fe68852da11996c660df1726aa66; `server/src/clientMessageHandler.ts` d7ae8235ee75de63b1716a4b67f27d69023f5d96; `server/src/httpServer.ts` ad5104a14233aa29834e008833a0f21458fc887b; `server/src/configPersistence.ts` 056e71dec89dd174df80d65da349cfafff57a5b7; `server/src/directories.ts` a27617b843733ff8eb5a6a0263324587bb57a32b; `server/src/characterProfilesPersistence.ts` 6444df2a8a032f0023426a226244d25cd7178a85; `server/src/cardOrder.ts` 3c4d7bd9397546dfe01081593045a9edaa57a0fa; `server/src/conversations.ts` bb698e3ce55862309d53320841550556d0d90b7f; `server/src/terminal/standaloneAgentLauncher.ts` f17562fcf673e5b8db4a7bd94d9ff953c4edf0b4; `adapters/vscode/PixelAgentsViewProvider.ts` 94d1ab2e1427377e67ff430a852d49863e5e4d87.

**Not performed:** fresh recursive tree retrieval (connector exposes no tree-fetch action in this session); tests/build/lint/typecheck; runtime/browser/VS Code; malformed payload, permission-failure or concurrent-client experiments. No source code changed.

**Next:** cross-layer lifecycle/reconnect and state reconciliation, preserving WV-PROTO-01/02/03 and WV-C08/F-08; do not repeat the completed 34-message matrix.


## 19. GOV-WEBVIEW-012 — Cross-layer lifecycle, reconnect and state reconciliation

**Status:** STATIC CROSS-LAYER TRACE COMPLETE; dynamic lifecycle remains OPEN.  
**Source:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; recursive tree SHA `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated=false`, 856 entries (739 blobs, 117 trees).  
**Scope:** standalone control-plane WebSocket reconnect and snapshot replay; VS Code webview readiness/buffering/restore; per-agent terminal data-plane reconnect; React/OfficeState reconciliation and lifecycle ownership. Existing message route and field-level matrices are reused, not repeated.

### 19.1 Coverage and source reconciliation

- Re-fetched the complete recursive Git tree. Webview ledger: 459/459 paths and blob SHAs match (0 missing, 0 extra, 0 mismatched). Adapter ledger: 8/8 paths and blob SHAs match. Server FILE-REFERENCE enumerates 135 paths but does not carry a per-file SHA manifest; server governance records its 135/135 historical reconciliation. All server files selected for this cross-layer trace match the current tree SHAs.
- No source file changed relative to the documented source commit. Unchanged reviewed files were revisited only for the named cross-layer lifecycle question.
- Selected source blobs: `webview-ui/src/hooks/useExtensionMessages.ts` 649025df90edef84d24c40556cee2ea007e15ccf; `webview-ui/src/transport/webSocketTransport.ts` 33008b95327f9e2a18e23aaf0f2060416c95aba9; `webview-ui/src/transport/onReconnect.ts` 030ae419587d5f3b69b78d74cf295811087e2bcd; `webview-ui/src/transport/postMessageTransport.ts` f9b622f6bc28eb18b3bb7131e10c596ad7e68acd; `webview-ui/src/transport/reconnectBackoff.ts` 44dc5aac6b52a73f35700509036a248396e3cc20; `webview-ui/src/App.tsx` 3eb91a2050da5f789223bc0a045a80fc841d60cb; `webview-ui/src/office/engine/existingAgents.ts` f23ff3f79f5fc6baf37f2915f8ff6abde107ce35; `webview-ui/src/office/engine/officeState.ts` 1afd778f4c72f1901ef91266e29a89f117e180c7; `webview-ui/src/terminal/terminalClient.ts` 55336df6e5d593008c0f1c7d34c8d0d0c5ae3d10; `webview-ui/src/hooks/useTerminalDrawer.ts` 2913c2331ad4bee5f127a249502a394bfb414980; `server/src/clientMessageHandler.ts` d7ae8235ee75de63b1716a4b67f27d69023f5d96; `server/src/httpServer.ts` ad5104a14233aa29834e008833a0f21458fc887b; `server/src/agentStateStore.ts` e73e5e47550cdfb1560ecdd0514b75be3688c5c1; `server/src/agentMessages.ts` df521f7a6d9ddbad8d7ee0c567df4054c500b9ea; `server/src/agentActivityResend.ts` f0c6fe190f9d259c6c172cfd7a4198ecc64dfb77; `adapters/vscode/PixelAgentsViewProvider.ts` 94d1ab2e1427377e67ff430a852d49863e5e4d87; `adapters/vscode/agentManager.ts` 30020b3e34d2d83fa4536e0d27f55c9e4fe3295c.

### 19.2 Standalone control-plane lifecycle

1. `WebSocketTransport` connects once at module creation. Close moves it to RECONNECTING and schedules the shared exponential backoff. The token is captured in the URL at construction and reused for reconnect.
2. `useExtensionMessages` registers the message handler, queues/sends the initial `webviewReady`, then subscribes through `onReconnect`. Every later CONNECTED transition sets `resyncPending` and sends another `webviewReady`.
3. The server sends connection access/capabilities, initial assets/settings, area mappings/directories, restores external agents, then sends `cardOrderLoaded`, `existingAgents`, `layoutLoaded`, activity replay, and live terminal-session announcements. This ordering is designed so restored characters exist before activity and terminal tabs are announced.
4. On a reconnect snapshot, the webview removes agents absent from the snapshot, detaches subagents, clears tool/status state, and then reconciles each incoming agent's look/seat. Activity is replayed from the server's current AgentState, not from missed historical broadcasts.
5. The terminal data plane is separate: one `TerminalConnection` per agent reconnects independently; the server PTY outlives the socket and sends a replay frame. Client terminal input is not queued while disconnected; last-known geometry is retained and re-sent on open.

### 19.3 VS Code webview lifecycle and initial hydration

- Each fresh `resolveWebviewView` resets `isWebviewReady=false` and clears the prior iframe's pending broadcast queue. The new iframe's `webviewReady` flushes buffered events, sends settings/area mappings, restores agents, starts scanners and asynchronously loads assets/layout. This is a new iframe lifecycle, not a WebSocket reconnect; `PostMessageTransport` reports CONNECTED continuously and has no reconnect event.
- A concrete ordering edge exists during restored-agent hydration: `restoreAgents` calls `store.set`; `AgentStateStore.set` emits `agentAdded`; the provider immediately sends `agentCreated` because `isWebviewReady` is already true. The webview's `agentCreated` handler immediately calls `OfficeState.addAgent`, even while `layoutReadyRef` is false. The provider then sends `existingAgents` using `adapter.loadSeats()`; because layout is not ready, `reconcileExistingAgents` buffers that metadata. When `layoutLoaded` flushes the pending item, `restoreAgent` calls `addAgent` again, but `OfficeState.addAgent` returns immediately when the ID already exists. Therefore the buffered snapshot metadata is not applied to that already-created character.
- **This extends existing WV-RESYNC-01; it is not a new finding ID.** If the separate persisted seat/look record differs from the AgentState fields in the earlier `agentCreated` event (for example, a seat saved through `saveAgentSeats`), the initial VS Code hydration can retain the event's earlier seat/look rather than the snapshot's authoritative metadata. The trigger is conditional; static source ordering is established, but a divergent persisted-record fixture/runtime reproduction is still needed.
- Existing adapter findings A-03 (split lifecycle ownership), A-05 (listener disposal/repeated view resolution) and A-06 (untracked restore grace timer) remain open and relevant. This session does not close or duplicate them.

### 19.4 Findings

#### WV-RESYNC-01 — Snapshot seat metadata may not be applied across layout/restore ordering (existing finding, extended)

- Preserve existing ID, severity and historical evidence. Existing reconnect trigger remains: on reconnect, `existingAgents` is reconciled against the currently loaded layout before the new `layoutLoaded` arrives; `syncAgent` cannot retain a requested seat absent from the old seat map, and the later layout rebuild does not retry that requested ID.
- Additional VS Code first-hydration trigger and evidence are described in §19.3: `PixelAgentsViewProvider.ts:675–806, 982`; `agentManager.ts:362–370, 495–531`; `agentStateStore.ts:115–120`; `agentMessages.ts:8–22`; `useExtensionMessages.ts:455–499`; `existingAgents.ts:55–100`; `officeState.ts:453–468`.
- **State:** OPEN; SOURCE-VERIFIED / STATIC-FINDING; not runtime reproduced. Remaining uncertainty: whether the separate adapter seat/look record diverges from AgentState in a reachable production path, and the visible frequency/impact.

#### WV-RESYNC-02 — Disconnected control-plane commands are replayed after reconnect

- **Severity:** Medium. **Confidence:** High. **State:** OPEN.
- **Affected files:** `webview-ui/src/transport/webSocketTransport.ts`; related `webview-ui/src/hooks/useExtensionMessages.ts`, `webview-ui/src/transport/onReconnect.ts`, and server ClientMessage handlers.
- **Evidence:** `webSocketTransport.ts:24, 60–70, 93–99` stores every ClientMessage in an unbounded `pendingMessages` array whenever the socket is not OPEN, then flushes the entire array on the next open. The queue is cleared only after flush or explicit transport disposal; no expiry, command classification, coalescing, acknowledgement, or cancellation is present. `onReconnect` sends `webviewReady` on the CONNECTED state transition; in `onopen`, state callbacks run before the pending queue flush.
- **Trigger / preconditions:** a UI action sends a ClientMessage while the control socket is connecting/reconnecting; the socket later reconnects before the transport is disposed.
- **Technical impact:** commands with non-idempotent or context-sensitive effects (notably `launchAgent`, `resumeConversation`, `closeAgent`, and provider/consent operations) can execute after a delay, after the user's context may have changed, or after the user has retried. Settings and persistence writes can also replay stale values. On reconnect the fresh snapshot is requested before the old queued commands are flushed, so the subsequent queued effects may immediately change state after the snapshot.
- **Verification:** SOURCE-VERIFIED / STATIC-FINDING; no network fault injection or duplicate-action runtime experiment.
- **Remaining uncertainty:** the exact set of controls reachable during a disconnect and user-visible behavior depends on UI state and timing; no runtime experiment was authorized.

### 19.5 Existing findings and ownership links preserved

- `WV-APP-01`: stale `isEditDirty` callback captured by the long-lived message subscription; still OPEN.
- `WV-TR-01`: subscriber exception aborts remaining WebSocket message dispatch; still OPEN.
- `WV-PROTO-01/02/03`: area-mapping fan-out, requester-only asset reload and false-success-on-write-failure; still OPEN.
- `WV-C08/F-08`: message runtime-validation gap; unchanged.
- Adapter `A-03/A-05/A-06` and server `F-01/F-03/F-05`: lifecycle ownership, subscription disposal, restore timer and shutdown boundaries; unchanged and not re-audited outside the named cross-layer trace.

### 19.6 Verification boundary and next action

- Static only. No tests, builds, lint, typecheck, browser/VS Code runtime, WebSocket drop/reconnect, PTY attach, fault injection or multi-client experiment was executed. No application source, test, asset, dependency or config was modified.
- The complete recursive tree was freshly retrieved in this session: 856 entries, 739 blobs, 117 trees, `truncated=false`. This supersedes the GOV-WEBVIEW-011 note that a fresh tree retrieval was unavailable at that earlier time; that historical note is not erased.
- Next: resolve the intended offline command policy and snapshot ordering through design evidence, then obtain explicit authorization for targeted runtime reproduction. Preserve WV-RESYNC-01/02, WV-APP-01, WV-TR-01, WV-PROTO-01/02/03 and A-03/A-05/A-06/F-01/F-03/F-05. Do not repeat the 34-message field matrix.

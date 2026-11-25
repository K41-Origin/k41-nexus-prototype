# Audit Evidence Log

Append-only session ledger. Corrections must be appended; do not erase historical entries.

## 2026-11-25 — GOV-CORE-001: core source and documentation re-verification

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Branch inspected:** `docs/hybrid-llm-engine-direction`  
**Branch tip inspected:** `13354f1a74b75b9ab3d8982544414134b1940ae3`  
**Historical core inventory root:** `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1`  
**Scope:** all tracked files under `core/` and all four core architecture documents.  
**Method:** recursive tree inventory + individual file retrieval + static cross-check of source and docs.

### Verified inventory

- Recursive tree: `truncated: false`.
- Total: **28 tracked files** = 1 YAML AsyncAPI specification + 27 TypeScript files.
- All 28 core blob SHAs at the branch tip match the historical core audit snapshot.
- Four core documents were retrieved and reviewed: README, MODULE-OVERVIEW, FILE-REFERENCE, PROTOCOL, DEPENDENCIES (five documents including README).
- All 27 TypeScript files and `core/asyncapi.yaml` were individually fetched. Large AsyncAPI spec was retrieved in line ranges.
- Core source was not modified.

### Verification limitations

- Static source/document review only.
- No install, test, typecheck, lint, build, AsyncAPI validation/generation, package inspection, browser/server runtime, or cross-host runtime trace was executed in this session.
- Consumer import claims that the core docs explicitly mark as inferred/open remain open.
- No claim is made that every contract has direct unit-test coverage.

### Corrections / confirmations recorded

1. **Confirmed:** core is not types-only. It contains runtime pure utilities and Node-specific filesystem/PNG asset tooling; `core/src/index.ts` header comment is inaccurate relative to its exports and package contents.
2. **Confirmed:** AsyncAPI YAML is the source of truth for generated `core/src/messages.ts`; generated file must not be edited manually.
3. **Confirmed:** raw terminal frames are a separate data plane from the AsyncAPI WebSocket control plane.
4. **Confirmed:** no upward imports from `server/`, `webview-ui/`, or `adapters/` are reported by the existing static core source audit. This is a source-import boundary, not proof of full package isolation.
5. **Confirmed as OPEN packaging risk:** `pngjs` is imported at runtime by `pngDecoder.ts` but root manifest classifies it as a devDependency. Bundle/package reachability must be checked before calling this a defect.
6. **Confirmed as OPEN:** exact public export consumption, duplicate/mirrored consumer types, architecture enforcement and full consumer graph need layer-level tracing.
7. **Confirmed:** binary/runtime and malformed-input behavior is not fully proven by static inspection alone.

### Core source blob SHA manifest

| Path | Blob SHA |
|---|---|
| `core/asyncapi.yaml` | `ff1247118819fe68852da11996c660df1726aa66` |
| `core/src/adapter.ts` | `af34de4cd31eaa0abff4298968b36c5a97de701f` |
| `core/src/assets/build.ts` | `08d04b03d8937558c437dc3de7a9dab06568aad3` |
| `core/src/assets/characterSheet.ts` | `d8070f7231d2bc26e3d7ae4ac061c44d0be0fb92` |
| `core/src/assets/colorUtils.ts` | `e65673cb8041c4097a1d17d013c7a2a04556dc9c` |
| `core/src/assets/constants.ts` | `600dfac29faca1b0de9a81d0901806e0454cf11e` |
| `core/src/assets/loader.ts` | `0cbd192c01398fa1f62fcdafd67f85a0882a1266` |
| `core/src/assets/manifestUtils.ts` | `236256dd55286644aa27be53194f65b199ae6828` |
| `core/src/assets/pngDecoder.ts` | `4a912de0553b57d71476e745fb4c4d4ce3553d3d` |
| `core/src/assets/seatedFrame.ts` | `dc6ee02154df275d64e18cf192821b1079a116e3` |
| `core/src/assets/types.ts` | `ca0391111e30954ca23703a432c565a4593a0289` |
| `core/src/characterProfiles.ts` | `cff5dd116b691471b054b9a74eac44fe51f3c68e` |
| `core/src/constants.ts` | `4da3ce4c4bfe12a95324fe8eb4fe468d4cf60ef7` |
| `core/src/defaultCharacterProfiles.ts` | `fefcf91d1656e563d4a5fdf81314f002616a5d0f` |
| `core/src/directoryCharacters.ts` | `70d6c30250a2272659d5bfb0cc28677828165379` |
| `core/src/index.ts` | `efc8aa0a5a7cf56b6a5ef939ac651e68bf2ff84d` |
| `core/src/isRecord.ts` | `c4cd1e8ac3180ed62b00350735efba16b693879c` |
| `core/src/localId.ts` | `f66ecf00483702c24ed76d8239fc61e654923d1f` |
| `core/src/messages.ts` | `2fd177f12327c03ae5bdbc4beb83360121c5f703` |
| `core/src/normalizeProjectPath.ts` | `cfb52f58238dccf21989c2dae2764ecfe30c4565` |
| `core/src/paletteUtils.ts` | `40b5c98d3bcd818e3355b827af969d55a4110bce` |
| `core/src/provider.ts` | `03c25878187aeeea8e710b1abde82c7efaab5ec9` |
| `core/src/schemas.ts` | `2a9a364da35a5f95bed8768cfb08f87a2b8e1942` |
| `core/src/teamProvider.ts` | `ec694d694068f47677b1052d4c1d747becbe8b2d` |
| `core/src/terminalAdapter.ts` | `f153ceeb6fad030d7287fc3bcaf41243e38797f9` |
| `core/src/terminalFrames.ts` | `e91f11eac94dac7f1c3f27950c5006e8fee5086c` |
| `core/src/transport.ts` | `1af71a0ed5bd0d4354680ea6f3fc7fa3b19ea168` |
| `core/src/wardrobeItems.ts` | `8ba819f78037b1a1b3bf0e0bce0073173e673ad4` |

### Core documentation blob SHAs

| Path | Blob SHA |
|---|---|
| `docs/current-architecture/core/README.md` | `074b4e618689f128ad233aeb39c8aa4c156743b0` |
| `docs/current-architecture/core/MODULE-OVERVIEW.md` | `2c940982b5835da6f459ebcd0e3f662318301fb5` |
| `docs/current-architecture/core/FILE-REFERENCE.md` | `42a8296499345a0cfe61c376236a617c7b782293` |
| `docs/current-architecture/core/PROTOCOL.md` | `159427ef825668a0c4d43c466716bb2d94031ec6` |
| `docs/current-architecture/core/DEPENDENCIES.md` | `d27fcf9ba861fe2fc8ec203ec9a2877e594635df` |

### Continuation pointer

Next module work remains governed by the register. For the active deep audit, resume `webview-ui/` at reconnect/snapshot resynchronization in `useExtensionMessages.ts`, then continue only the recorded evidence gaps. Do not reopen completed `core/` source review unless a blob SHA changes or a concrete cross-module question requires it.


## 2026-11-25 — GOV-CORE-002: current-tip core inventory closure check

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Branch:** `docs/hybrid-llm-engine-direction`  
**Commit inspected:** `c522dbdac525b226ec84afa5b7c829289ea1949f`  
**Scope:** complete tracked `core/` subtree; reconciliation against the prior source SHA manifest and core file-reference ledger.

### Results

- Retrieved the recursive Git tree at the inspected commit; `truncated: false`.
- Counted exactly **28 tracked files**: 1 `core/asyncapi.yaml` and 27 `.ts` files under `core/src/`.
- Individually fetched all 28 paths from this exact commit. Every fetch returned non-empty content.
- Compared every returned blob SHA with the recorded 28-file manifest from GOV-CORE-001: **28/28 match**.
- Reconciled the 27 TypeScript paths and the AsyncAPI YAML path against `docs/current-architecture/core/FILE-REFERENCE.md`; no tracked core path is absent from the file reference.
- No new or changed core source file was found. No source file was modified.

### Audit status

**CORE SOURCE INVENTORY + STATIC REVIEW: COMPLETE for the verified 28-file snapshot.** No file was missed from the recorded core inventory.

This closure check does not upgrade the remaining cross-layer, package/bundle, test-coverage, AsyncAPI generation, or runtime questions. No tests, build, typecheck, lint, package inspection, AsyncAPI validation/generation, or runtime execution were performed.

### Continuation

Core is not the active source-review target unless its blob SHA changes or a concrete cross-layer question requires a narrowly scoped revisit. Proceed to the already-recorded server audit evidence without repeating its source review; preserve findings F-01 through F-08 and resolve only documented gaps.


## 2026-11-25 — GOV-SERVER-001: server inventory and documentation reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Branch:** `docs/hybrid-llm-engine-direction`  
**Verification commit:** `d571b69d03b6993ab14d66f0d11d5a3d79a52a5b`  
**Scope:** complete tracked `server/` subtree and all server architecture documents; reconciliation only, no repeat of unchanged source review.

### Tree and source identity

- Recursive tree: `truncated: false`.
- Current inventory: **135 blobs** = 74 `server/src/` + 57 `server/__tests__/` + 4 root support/config.
- Compared path/blob-SHA pairs against historical snapshots `f1af3e0c36573e4317143969cf43dde642877ba6`, `89851b12b968fcf30288f1edf4315e6377d51325`, and `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- All three historical snapshots and current verification commit contain the same 135 paths and identical blob SHAs: **135/135 unchanged**. No server file was added, removed, or content-changed.
- Prior audit evidence remains applicable: 74/74 runtime source files fetched/reviewed; 57/57 test/support files fetched and inventoried; 4/4 root support/config files fetched/reviewed.
- `FILE-REFERENCE.md` reconciles all 135 tracked paths across its runtime, test/support and root support/config sections.

### Documentation

Fetched and confirmed non-empty all nine server docs: README, MODULE-OVERVIEW, FILE-REFERENCE, DEEP-AUDIT, RUNTIME-LIFECYCLE, PROVIDERS, PERSISTENCE, HTTP-WEBSOCKET and DEPENDENCIES.

README's older inventory arithmetic/baseline wording was inconsistent with DEEP-AUDIT and FILE-REFERENCE. Since the compared trees prove identical server blob identities, README metadata is normalized to the canonical audit baseline and 74/57/4 partition; the prior snapshot remains recorded as equivalent.

### Status and limits

- Server static source inventory/review: complete for the verified snapshot; no tracked file missed.
- Test/support files: fetched and inventoried, not all assertions exhaustively reviewed and **not executed**.
- Findings F-01–F-08 preserved as open / partially covered per DEEP-AUDIT; none closed by this reconciliation.
- No tests, build, typecheck, lint, live network test, OS PTY matrix or runtime experiment executed.
- No application source changed. Governance records and server README inventory metadata only.

### Continuation

Use `governance/modules/server.md` as governance summary, `server/DEEP-AUDIT.md` as detailed finding owner, and `server/FILE-REFERENCE.md` as per-file inventory. Do not duplicate the 135-file manifest or F-01–F-08 analysis in register/evidence log. Reopen unchanged source only for a concrete cross-module question, changed SHA, or authorized verification.


## 2026-11-25 — GOV-WEBVIEW-001: webview-ui inventory and governance reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Branch:** `docs/hybrid-llm-engine-direction`  
**Verification commit:** `8fc045de9ddae905cc131498d918ddbd64cc8bca`  
**Verification tree:** `6c65c2127b2fdf3742f76b317d43e9faccb61506`  
**Historical source baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Scope:** full tracked `webview-ui/` subtree, existing webview architecture documentation, and the recorded reconnect/snapshot continuation question.

### Tree and ledger reconciliation

- Recursive tree: `truncated: false`.
- Inventory: **459 blobs** = 302 `src/`, 92 `public/`, 54 `test/`, 11 root/package/build/config.
- Compared current tree with the baseline tree (`2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`): **0 added, 0 removed, 0 changed blob SHAs**.
- Reconciled current tree against `FILE-REFERENCE.md`: **459/459 exact path + blob SHA matches**, 0 missing, 0 extra, 0 mismatches.
- The ledger status distribution is 143 `REVIEWED`, 61 `FETCHED / REVIEWED REPORTED`, 57 `REVIEWED REPORTED`, 54 `FETCHED / INVENTORIED`, 143 `BINARY HEADER VERIFIED`, and 1 `NOT VERIFIED`. These sum to 459. Reported-review and inventoried states are not silently upgraded by tree reconciliation.
- All ten webview architecture documents were fetched and confirmed non-empty: README, FILE-REFERENCE, DEEP-AUDIT, ASSET-INVENTORY, PROTOCOL-MATRIX, MODULE-OVERVIEW, STATE-MANAGEMENT, RENDERING-ENGINE, DEPENDENCIES and TRANSPORT.

### Targeted reconnect/snapshot source trace

The following relevant source files were fetched at the verification snapshot:

| Path | Blob SHA | Scope |
|---|---|---|
| `webview-ui/src/hooks/useExtensionMessages.ts` | `649025df90edef84d24c40556cee2ea007e15ccf` | Ranges 1–90 and 250–980; reconnect setup, snapshot and layout handling |
| `webview-ui/src/transport/onReconnect.ts` | `030ae419587d5f3b69b78d74cf295811087e2bcd` | Full helper |
| `webview-ui/src/office/engine/existingAgents.ts` | `f23ff3f79f5fc6baf37f2915f8ff6abde107ce35` | Reconciliation and stale-agent logic |
| `webview-ui/src/office/engine/officeState.ts` | `1afd778f4c72f1901ef91266e29a89f117e180c7` | Layout rebuild, seat sync and subagent detach |
| `server/src/clientMessageHandler.ts` | `d7ae8235ee75de63b1716a4b67f27d69023f5d96` | Standalone `webviewReady` snapshot ordering |
| `webview-ui/test/onReconnect.test.ts` | `344ffa982c77b11cba8529942ce55cc18febff83` | Reconnect callback unit-test inventory |
| `webview-ui/test/existingAgents.test.ts` | `11914967532cda8fa8bc62e84613e5f56bfdb80a` | Agent reconciliation assertions |
| `webview-ui/test/subagentResync.test.ts` | `186ac30aa58919e0affda0b8a25ba849bb3825a6` | OfficeState subagent resync assertions |

Static trace: reconnect sets `resyncPending` and sends `webviewReady`. Standalone `handleWebviewReady` emits `existingAgents` before `layoutLoaded`, then replays activity. Because the webview already has a loaded layout during reconnect, existing agents are reconciled immediately against the old seat map before the new layout is applied.

### Finding recorded

- `WV-RESYNC-01`: possible stale-seat retention when the server snapshot names a seat absent from the client's pre-reconnect layout, while the client's old seat remains in the incoming layout. The static control flow supports the risk; no runtime reproduction was performed. Detailed evidence, preconditions, impact and uncertainty are recorded in `webview-ui/DEEP-AUDIT.md`.

### Verification boundaries

- Static source/document inspection and Git tree/blob comparison only.
- No tests, build, lint, typecheck, protocol generation, browser, VS Code host, WebSocket fault injection or runtime experiments executed.
- The test files above were read, not executed.
- No application source, assets, package manifest or configuration changed. Only audit documentation and governance records are included in this documentation commit.

### Continuation

Continue with the exact line-coverage gaps in the 118 reported-review rows, the 54 test files inventoried but not fully reconciled, the unverified TTF font, and the ClientMessage/ServerMessage producer-consumer/validation matrix. Preserve existing WV findings and do not repeat unchanged files already reviewed for the same question.


## 2026-11-25 — GOV-WEBVIEW-002: incremental webview static source review

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Documentation branch parent:** `3c6145079339591767998e548fa79f93a2ee3336`  
**Scope:** SHA-first verification of the full `webview-ui/` subtree; complete static review of 53 previously reported source files; targeted revisit of two terminal data-plane files; assertion review of five tests; comparison with all ten webview architecture documents.

### Repository and inventory

- `main` and the audit documentation branch both contain 459 webview blobs.
- Exact path/blob-SHA comparison against the canonical baseline and between branches: 0 added, 0 removed, 0 changed.
- The complete 459-row file ledger remains the inventory owner. No file is missing from the inventory.
- All ten webview architecture documents were fetched and compared with the implementation areas reviewed.

### Files reviewed

- Complete content was re-fetched and statically reviewed for 55 source files. 53 were advanced from reported-review to `REVIEWED`; two terminal data-plane files were already `REVIEWED` and were revisited for cleanup/lifecycle correlation.
- The exact 53 path/SHA/size rows are updated in `webview-ui/FILE-REFERENCE.md`; the two revisited rows retain their prior status.
- Large-file ranges were complete: `App.tsx` 1–1031; `useExtensionMessages.ts` 1–997; `CharactersView.tsx` 1–448; `PixelEditor.tsx` 1–471; `Stage.tsx` 1–297; `TerminalPane.tsx` 1–270; `useTerminalDrawer.ts` 1–241.

### Test files inspected

| Path | Blob SHA | Evidence |
|---|---|---|
| `webview-ui/test/existingAgents.test.ts` | `11914967532cda8fa8bc62e84613e5f56bfdb80a` | Full assertions inspected; not executed |
| `webview-ui/test/subagentResync.test.ts` | `186ac30aa58919e0affda0b8a25ba849bb3825a6` | Full assertions inspected; not executed |
| `webview-ui/test/onReconnect.test.ts` | `344ffa982c77b11cba8529942ce55cc18febff83` | Full assertions inspected; not executed |
| `webview-ui/test/castSync.test.ts` | `2a0c507d8173811995c8b6578079d29b20acdc04` | Full assertions inspected; not executed |
| `webview-ui/test/characterRenderer.test.ts` | `618af4fec0b4968feae3bddee3f4e16d64d42da4` | Full assertions inspected; not executed |

### Static observations

1. The main architecture documents align with the source reviewed: cast store/draft flow, wardrobe/sheet composition, Canvas/DPR sizing, terminal control/data-plane separation, and desktop/mobile terminal pane mounting.
2. No new source/document contradiction requiring a new finding ID was established.
3. Additional exact evidence for existing `WV-C03/WV-C06`: `useTerminalDrawer.ts:150–168` registers window-level resize-drag listeners and sets body `userSelect`; cleanup is only in the mouseup callback, with no hook-unmount cleanup. Static lifecycle gap only; no runtime reproduction.
4. The five reconnect/cast/renderer test files were inspected, not executed. In particular, the seat tests cover a requested seat already present in the current map; they do not cover the stale-layout seat condition recorded by `WV-RESYNC-01`.

### Updated coverage

- Source review: 196 `REVIEWED`; 65 reported-review rows remain.
- Tests: 5/54 complete assertion inspections recorded; 49 remain inventory-only.
- Binary evidence: 143 header/metadata-only rows; TTF remains unverified.
- No tests, build, lint, typecheck, protocol generation, runtime experiment, application source change or asset change occurred.


## 2026-11-25 — GOV-WEBVIEW-003: browser mock and development asset pipeline review

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Documentation branch parent:** `d15dbb41666b2ac7420e4ebe88456c250c10298f`  
**Scope:** browserMock bootstrap/asset decoding, Vite asset middleware/cache invalidation, and two related integration test files.  
**Method:** SHA-first source fetch, full-file static inspection, source/test cross-reference. No execution.

### Files fully reviewed

| Path | Blob SHA | Lines | Status |
|---|---|---:|---|
| `webview-ui/src/browserMock.ts` | `335caac9b428de7801b0c5c680eaab2dd7a1f290` | 1–248 | REVIEWED |
| `webview-ui/src/main.tsx` | `223bbf51073edc73b9646be0e9618b11ea1ac250` | 1–27 | REVIEWED |
| `webview-ui/vite.config.ts` | `b47531773bf6fea57fe6d46a6291133371aa56ea` | 1–126 | REVIEWED |
| `webview-ui/test/dev-assets.test.ts` | `44c181de7e7f63904143d6e7ccdf3b0fc33bb0b7` | 1–99 | TEST-INVENTORIED |
| `webview-ui/test/build-subpath.test.ts` | `6531f84f343232b656cd9265d701a478babd4277` | 1–137 | TEST-INVENTORIED |

Cross-reference source:
- `core/src/assets/loader.ts` — `0cbd192c01398fa1f62fcdafd67f85a0882a1266`.
- `core/src/assets/build.ts` — `08d04b03d8937558c437dc3de7a9dab06568aad3`.

### Results

1. The entrypoint awaits browserMock initialization only for browser runtime + Vite DEV; production standalone and VS Code do not import it through this entrypoint.
2. The dev mock prefers decoded floor/wall/furniture endpoints and falls back to browser PNG decoding if the full trio is unavailable. Carpet endpoint failure is intentionally degraded to an empty array.
3. The mock dispatch order sends furniture assets before layout.
4. The Vite asset plugin clears its decoded cache and sends full reload on watcher `change`; no add/unlink listener was found in the inspected implementation. This adds source evidence to existing `WV-VITE-01`; no new ID was created.
5. `dev-assets.test.ts` checks root/subpath metadata and representative asset URLs but omits the decoded carpets endpoint and does not exercise browserMock fallback/dispatch or watcher invalidation. `build-subpath.test.ts` checks built bundles, metadata sidecars and manifest icons, not browserMock execution.
6. The browserMock file comment suggests a plain-browser production fallback, but the inspected main entrypoint gates import to DEV. Actual production reachability of that fallback is not established by this code path.

### Coverage delta and limits

- 3 source rows advanced to REVIEWED: browserMock.ts, main.tsx, vite.config.ts.
- 2 test rows advanced to TEST-INVENTORIED: dev-assets.test.ts, build-subpath.test.ts.
- New status distribution: 199 REVIEWED source rows; 62 reported-review source rows; 7 TEST-INVENTORIED tests; 47 FETCHED / INVENTORIED tests; 143 binary header-only rows; 1 unverified TTF.
- No tests/build/lint/typecheck/protocol generation/runtime or watcher experiment executed.
- No application source, asset, dependency or configuration file modified.


## 2026-11-25 — GOV-WEBVIEW-004: reported-review historical evidence reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch/baseline:** `main` / `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Documentation branch starting point:** `docs/hybrid-llm-engine-direction` / `2614502f8983977ed850c1a8a9b840689c6aa183`  
**Scope:** all 62 `FETCHED / REVIEWED REPORTED` and `REVIEWED REPORTED` source rows in webview-ui FILE-REFERENCE.  
**Method:** re-read governance and audit history; inspect historical DEEP-AUDIT and FILE-REFERENCE revisions; compare each reported-review path/blob SHA against the complete source tree. No application source was modified.

### Repository reconciliation

- Recursive tree: `truncated: false`.
- `webview-ui/`: 459 blob entries.
- Reported-review subset: 62 paths.
- Path/SHA comparison: 62 present, 62 SHA matches, 0 missing, 0 changed.

### Historical evidence result

The historical DEEP-AUDIT contains subsystem-level review claims but no file-by-file line-range manifest for these 62 rows. It explicitly records that some large UI fetch outputs were truncated and their corresponding ledger entries remain reported-review. Component/hook coverage is described by broad groups with variable exact coverage. Historical FILE-REFERENCE revisions preserve the reported-review statuses; the examined documentation commit titled `close component tail coverage gaps` did not add per-file line-range evidence for these rows.

### Row breakdown

| Group | Rows | Outcome |
|---|---:|---|
| `src/characters/ui/` | 9 | Historical group review reported; exact file coverage not evidenced |
| `src/components/` | 32 | Prior inspection reported; exact line coverage/cross-references not evidenced |
| `src/hooks/` | 14 | Prior inspection reported; lifecycle/cross-module gaps remain |
| Other source files | 7 | Historical review claim reported; exact line coverage not evidenced |
| **Total** | **62** | **SHA-reconciled; no row promoted to REVIEWED** |

### Decision

This session closes the historical **reconciliation** question, not the source-content review. All 62 rows remain in their reported-review statuses because the available historical evidence is insufficient to certify complete per-file coverage. FILE-REFERENCE rows now explicitly record that their SHA is reconciled while line-level verification remains open. This preserves historical work without converting an unverified claim into a verified review.

### Execution limits

- No 62-file full source reread was claimed or performed in this session.
- No tests, build, lint, typecheck, or runtime experiment executed.
- No application source, assets, configuration or dependencies modified.


## 2026-11-25 — GOV-WEBVIEW-005: complete components deep static review

### Governance and repository state

- Governance reviewed before source review and documentation updates: governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, webview README, DEEP-AUDIT and FILE-REFERENCE.
- Documentation branch at session start: `docs/hybrid-llm-engine-direction`, commit `9c64638df8d4048f404e62193a3fbe1327d88252`.
- Source branch: `main`, commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive source tree: `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated: false`.
- The `webview-ui/src/components/` tree contains 54 blobs. FILE-REFERENCE contains 54 corresponding component rows: 0 missing, 0 extra and 0 blob-SHA mismatches.

### Files fully re-fetched and reviewed

GOV-WEBVIEW-004 had left 32 component rows as reported-review because exact per-file line evidence was not present in the historical records. This session re-fetched the full content and performed static review for all 32. Exact path, blob SHA and line range are recorded in [DEEP-AUDIT.md](../webview-ui/DEEP-AUDIT.md#12-incremental-static-review--gov-webview-005), and each corresponding FILE-REFERENCE row is now REVIEWED.

The 32 files are:
- `directoryStrip.ts`, `introBubbleGeometry.ts`, `loadingSceneArt.ts`, `loadingSceneLayout.ts`, `MigrationNotice.tsx`, `MobileAgentBar.tsx`, `MobileKeyBar.tsx`, `statusDot.ts`.
- `TerminalHideRibbon.tsx`, `Tooltip.tsx`.
- All 18 files under `components/ui/`.
- `useCardReorder.ts`, `VersionIndicator.tsx`, `VisualColorPicker.tsx`, `ZoomControls.tsx`.

The other 22 component files already had REVIEWED status and were not repeated. Therefore the component subtree is now 54/54 REVIEWED.

### Findings and future priorities

- `WV-C12` — `useCardReorder.ts:150–170`: the same completion handler is registered for `touchend` and `touchcancel`; cancellation may be treated as tap or drop depending on gesture phase. STATIC-FINDING / OPEN.
- `WV-C13` — `components/ui/Modal.tsx:24–41, 57–88`: Escape stacking exists, but the rendered panel has no visible dialog role/aria-modal declaration and the component contains no focus entry/trap/return lifecycle. STATIC-FINDING / OPEN.
- `WV-C14` — `loadingSceneArt.ts:61–95` and `LoadingScene.tsx:118–124`: the cached load Promise can remain rejected and the component attaches no rejection handler/fallback in the inspected path. STATIC-FINDING / OPEN.
- Additional evidence for existing `WV-C03/WV-C06`: `useCardReorder.ts:175–210` installs window mouse listeners and removes them only from mouseup; the unmount cleanup clears only the timer. Existing finding IDs retained.

These are audit observations and future technical priorities only. No fixes were attempted and no runtime behavior was claimed as reproduced.

### Updated coverage and limits

- Ledger rows promoted: 32.
- Component subtree: 54/54 REVIEWED (22 previously REVIEWED + 32 newly verified).
- Remaining reported-review rows across webview source: 30 (14 hooks, 9 character UI files, 7 other source files).
- Existing 47 FETCHED / INVENTORIED test rows remain unreviewed at assertion level; test execution remains separate.
- No tests, build, lint, typecheck, protocol generation, browser/VS Code runtime or accessibility experiment executed.
- No application source, assets, configuration, dependencies or generated files modified.

### Continuation

Continue with the remaining 30 reported-review source rows, prioritizing the 14 hooks and their lifecycle/cross-module behavior. Then continue the 47 test assertion reviews, protocol producer-consumer tracing, host lifecycle/reconnect analysis and outstanding binary/font evidence. Do not repeat the 54 component files unless their source SHA changes or a new cross-module question requires it.


## 2026-11-25 — GOV-WEBVIEW-006: webview hooks direct source verification

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree SHA:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Documentation branch before this session's publication:** `docs/hybrid-llm-engine-direction` at `c24f2b79b037909092167a5d361b20c7e30f7547`  
**Scope:** all tracked files under `webview-ui/src/hooks/`.

### Governance and historical reconciliation

- Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, DEEP-AUDIT and FILE-REFERENCE.
- Complete source tree confirms **24 hook files**, not 14 total. The earlier module record's 14 was the remaining reported-review gap, not the total hook count.
- Reconciled all 24 paths and blob SHAs against FILE-REFERENCE: 0 missing, 0 extra, 0 SHA mismatches.
- Ten hook files were already REVIEWED under GOV-WEBVIEW-002 and were not redundantly inspected.
- Individually re-fetched and statically reviewed all 14 remaining reported-review files. All 24 hooks are now REVIEWED at this source commit.
- Updated per-file ledger line coverage for the 14 newly reviewed files.

### Findings

- Added `WV-C15`: `useEditorKeyboard` global keydown handler does not exclude text inputs; App activates it during editor mode while EditorToolbar has Area name and rename text fields. Static only.
- Added `WV-C16`: redo shortcut branch compares lowercase `e.key === 'z'` while requiring Shift; standard Shift+Z key value is uppercase. Ctrl/Cmd+Y remains separate fallback. Static only.
- Existing findings and IDs preserved; no prior finding was closed or reused.

### Execution and modification limits

- Static source/documentation investigation only.
- No tests, builds, lint, typecheck, browser/VS Code runtime, keyboard simulation, or other runtime experiment executed.
- No application source, test, configuration, dependency or asset file changed.
- Findings are not runtime reproduced and no fixes were implemented.

### Next continuation

- 16 reported-review source rows remain: 9 `characters/ui/` files and 7 other source files.
- After those, continue the 47 inventoried-only tests, protocol producer-consumer tracing, host lifecycle/reconnect and binary/font evidence.


## 2026-11-25 — GOV-WEBVIEW-007: character subsystem complete source review

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree SHA:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction` at `061e968ddc37aed8e062abb3c40a58990b8d0c77`  
**Scope:** all 115 tracked files under `webview-ui/src/characters/`.

### Governance and historical reconciliation

- Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, DEEP-AUDIT and FILE-REFERENCE.
- Tree-to-ledger reconciliation: 115/115 paths and blob SHAs match; 0 missing, 0 extra, 0 mismatches.
- Breakdown: 33 source files, 81 PNG art assets, 1 wardrobe JSON.
- Nine `REVIEWED REPORTED` UI files were fetched in full and statically reviewed; exact byte/SHA identity and line counts were recorded. All 33 source files now have REVIEWED evidence (including one file whose earlier review and EOF boundary evidence were retained).
- The 81 PNGs remain at historical BINARY HEADER VERIFIED level (signature and 128×96 IHDR); full CRC/pixel validation was not performed. The wardrobe manifest remains historically parsed and consistency-checked.

### Cross-reference observations

- Character view keyboard shortcuts explicitly exclude text-entry targets; this does not alter or close `WV-C15`, which concerns the separate editor shortcut hook.
- Pointer/undo-step listeners, RAF animation, ResizeObserver and document pointerdown listeners have explicit cleanup paths in the inspected UI.
- Custom-item import catches PNG decode errors and revokes object URLs in `finally`. Existing wardrobe aggregate decode rejection risk remains `WV-C11` / `WV-WARD-01`.
- No new stable finding ID was added; existing findings and states were preserved.

### Execution and modification limits

- Static source/document review only.
- No tests, build, lint, typecheck, runtime, accessibility, pointer simulation, image decode or visual regression was executed.
- No application source, test, configuration, dependency or asset file changed.

### Next continuation

- 7 reported-review source rows remain elsewhere in `webview-ui/src/`.
- 7 `FETCHED / REVIEWED REPORTED` rows remain as a separate exact-coverage evidence gap.
- 47 inventoried-only tests, protocol producer-consumer tracing, host lifecycle/reconnect and binary/font evidence remain open.


## 2026-11-25 — GOV-WEBVIEW-008: remaining webview source coverage

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Recursive tree SHA:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated: false`)  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction` at `8aabad19e60226ef44ecfc94c5aba7c427616132`  
**Scope:** the seven `FETCHED / REVIEWED REPORTED` source rows remaining in `webview-ui/src/`.

### Governance and source reconciliation

- Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, webview module record, DEEP-AUDIT and FILE-REFERENCE before inspecting source.
- Complete recursive tree: `truncated: false`; 459 webview blobs.
- FILE-REFERENCE reconciliation: 459/459 paths and blob SHAs match; 0 missing, 0 extra, 0 mismatches.
- The seven ledger rows were fetched in full and statically reviewed: `changelogData.ts` (341 lines), `constants.ts` (880), `index.css` (697), `loadingPreview.tsx` (14), `notificationSound.ts` (128), `runtime.ts` (28), and `testHooks.ts` (352). All current SHAs match the ledger.
- Clarification: the earlier continuation text referred to seven remaining source rows and seven fetched/reported-review rows as if separate. Ledger reconciliation shows these are the same seven rows; no second seven-file group existed.

### Finding

- Added `WV-C17`: `index.css` does not suppress the infinite `.pixel-pulse` and `.usage-heart-beat` animations under `prefers-reduced-motion: reduce`. Source and consumers are cross-referenced in the owning deep-audit record. Static only; no browser accessibility test executed.

### Execution and modification limits

- Static source/document investigation only.
- No tests, build, lint, typecheck, browser/VS Code runtime, accessibility audit or audio experiment executed.
- No application source, test, configuration, dependency or asset file changed.
- Seven source rows promoted to `REVIEWED`; the 47 inventoried-only tests, protocol/lifecycle traces and binary/font validation remain open.

### Next continuation

- Review the 47 `FETCHED / INVENTORIED` test files at assertion level without executing them.
- Continue ClientMessage/ServerMessage producer-consumer tracing and host lifecycle/reconnect analysis.
- Retain the 143 binary-header-only assets and one unverified font as open validation limits.


## 2026-11-25 — GOV-GOVERNANCE-001: audit continuity and no-duplication governance update

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Documentation branch inspected before update:** `docs/hybrid-llm-engine-direction` — `e9bce038d8cf7d32640148ebacb97c49298fe796`  
**Source branch state checked:** `main` — `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Scope:** governance documentation only; no application source investigation or execution.

### Governance/history reviewed

- Governance README, AUDIT-REGISTER, AUDIT-METHOD and EVIDENCE-LOG.
- Core, server and webview governance records; webview DEEP-AUDIT and FILE-REFERENCE.
- Adapter README, DEEP-AUDIT and FILE-REFERENCE to reconcile the actual completed adapter audit against the stale register status.
- Existing documented history through GOV-WEBVIEW-008 and adapter findings A-01–A-10.

### Corrections and continuity controls

1. Added a dedicated adapter governance record. The register had incorrectly described `adapters/` as NOT AUDITED despite the adapter deep audit documenting 8/8 source files reviewed and findings A-01–A-10. The new record explicitly labels this as DOC-RECORDED and leaves current-source SHA reconciliation OPEN.
2. Corrected the webview governance session range to include GOV-WEBVIEW-008 and distinguished the historical source baseline from the later source verification snapshot at `56f354f...`.
3. Replaced stale register continuation text that still requested review of seven source rows already completed in GOV-WEBVIEW-008. The actual remaining 47 assertion-level test reviews, protocol tracing, lifecycle/reconnect and binary/font gaps are now explicit.
4. Added a mandatory SHA-first continuation gate: record source/docs refs separately, compare complete-tree path/blob identities, preserve historical baselines, inspect only changed or explicitly open files, distinguish test inventory from execution, and search existing findings before assigning IDs.
5. Linked the adapter record from governance README and register. Historical audit entries and finding IDs were preserved.

### Verification limits

- This was a documentation reconciliation/update, not a new source audit.
- No source tree was fetched or compared in this session; no current adapter SHA claim is made.
- No tests, build, lint, typecheck, runtime, browser or VS Code host experiments were executed.
- No application source, assets, manifests or configuration were changed.

### Continuation pointer

Resume with the 47 webview test files still marked `FETCHED / INVENTORIED` for assertion-level review, unless the user selects another scope. Do not repeat unchanged `REVIEWED` source files. Before any adapter source continuation, reconcile its 8 blob SHAs against the active source tree.

## 2026-11-25 — GOV-ADAPTER-001: active-source baseline and SHA reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main` — `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction` — `f9a0af11b1ba48885dcc553e58bd8470478752a1`  
**Historical adapter source baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Scope:** complete repository tree baseline plus adapter path/blob SHA reconciliation; no source re-review.

### Repository baseline

- Repository default branch: `main`.
- Source recursive tree: 856 entries, 739 blobs; `truncated: false`.
- Documentation recursive tree: 918 entries, 790 blobs; `truncated: false`.
- Shared source paths between `main` and the documentation branch: 739/739 have identical blob SHAs; 0 changed; 0 source-only paths; 51 documentation-branch-only blobs.
- The 51 extra blobs are documentation-branch additions; no application source blob differs between the two refs.

### Module SHA reconciliation against active source

| Module | Historical baseline | Baseline blobs | Current blobs | Added | Removed | Changed SHA |
|---|---|---:|---:|---:|---:|---:|
| `core/` | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | 28 | 28 | 0 | 0 | 0 |
| `server/` | `f1af3e0c36573e4317143969cf43dde642877ba6` | 135 | 135 | 0 | 0 | 0 |
| `adapters/` | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | 8 | 8 | 0 | 0 | 0 |
| `webview-ui/` | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | 459 | 459 | 0 | 0 | 0 |

- All four module subtrees were compared by exact path/blob-SHA pairs between their recorded historical source baseline and current `main`; all paths and blob identities are unchanged.
- Independently compared all 459 webview ledger rows with current `main`: 459/459 paths and SHAs match; 0 missing, 0 extra, 0 mismatches. Status distribution remains 261 REVIEWED, 7 TEST-INVENTORIED, 47 FETCHED / INVENTORIED, 143 BINARY HEADER VERIFIED, 1 NOT VERIFIED.
- Adapter current path/blob SHA pairs are recorded in `adapters/FILE-REFERENCE.md`; 8/8 equal the historical baseline. This closes the adapter SHA-reconciliation gate without repeating its static review.

### Interpretation and limitations

- **TREE-VERIFIED:** complete tree inventories and exact path/blob comparisons above.
- **DOC-RECORDED:** prior core/server/adapter/webview source review coverage and findings remain documented; unchanged SHA preserves content identity, not runtime correctness.
- No source files were re-fetched for semantic review in this session; no application source was modified.
- No tests, build, lint, typecheck, protocol generation, or runtime experiment was executed.
- Existing findings remain open; this session creates no new code finding and does not close any technical finding.

### Documentation discrepancy noted

The audit register's adapter continuation gate was stale: it requested SHA reconciliation even though this session now completes it. The register and adapter module record were updated to point to cross-module lifecycle verification instead. The evidence log had no dedicated adapter-session entry; this entry supplies the missing append-only reconciliation evidence.

### Next continuation point

Continue the 47 webview test files still marked `FETCHED / INVENTORIED` at assertion level, or select a specific cross-module protocol/lifecycle trace. Do not repeat unchanged reviewed source files. Adapter A-01–A-10 remain static findings pending authorized dynamic verification.


## 2026-11-25 — GOV-WEBVIEW-009: assertion-level review of 47 inventoried tests

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main` — `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, recursive `truncated: false`  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction` — `fd998227336bb3f196d0a26caeea72dc96d7a9a0`  
**Scope:** all 47 test files previously `FETCHED / INVENTORIED` under `webview-ui/test/`. Their paths and blob SHAs are recorded in the updated webview `FILE-REFERENCE.md`; complete assertion maps are in `webview-ui/DEEP-AUDIT.md` section 16.

### Work performed

- Read current governance and webview audit records before source inspection.
- Reconciled the current webview inventory against the 459-row ledger; the active source tree is complete and the 47 test file SHAs match their recorded identities.
- Individually fetched and read all 47 test sources, including helper/setup code and assertions. The complete file-by-file assertion scope is documented in DEEP-AUDIT section 16.
- Promoted all 47 rows from `FETCHED / INVENTORIED` to `TEST-INVENTORIED`. With the 7 previously TEST-INVENTORIED files, the resulting status is 54/54 test files reviewed at assertion level; 0 remain FETCHED / INVENTORIED.
- Added `WV-TEST-01`, a low-severity static test-quality finding: `officeBlink.test.ts` compares sequential simulations sharing one already-advanced PRNG, rather than interleaving two independently initialized blink clocks. The production blink implementation is not thereby declared defective.
- Recorded coverage limits and correlations with existing `WV-C08`, `WV-TR-01`, `WV-RESYNC-01`, `WV-C12` and other open findings; no duplicate issue IDs were created.

### Evidence classification and limits

- **TREE-VERIFIED:** complete source tree and 47 test paths/blob identities.
- **SOURCE-VERIFIED:** test source content fetched and reviewed at the stated source commit; `officeBlink.test.ts` lines 25–38, 71–75 and `blink.ts` lines 29–50 support WV-TEST-01.
- **TEST-INVENTORIED:** all 47 formerly inventoried-only test files now have assertion-level review evidence.
- **OPEN:** tests were not executed; assertions do not prove runtime behavior or integration coverage.
- No npm/Vitest command, build, lint, typecheck, browser/VS Code host, mobile device or accessibility experiment was run.
- No application source or test file was modified.

### Consolidated status

- Webview: 459/459 path/SHA reconciled.
- Source status: 261 REVIEWED; no reported-review source rows remain.
- Tests: 54 TEST-INVENTORIED; 0 FETCHED / INVENTORIED.
- Assets: 143 BINARY HEADER VERIFIED; 1 NOT VERIFIED font.
- Existing findings remain open; `WV-TEST-01` is added and open.

### Next continuation point

Continue UI → adapter → server ClientMessage/ServerMessage tracing, field validation, authorization, persistence, side effects and handshake order. Then continue targeted lifecycle/reconnect and binary/font verification. Do not re-read unchanged REVIEWED source or TEST-INVENTORIED test files for the same question. Dynamic test execution remains outside scope unless explicitly authorized.


## 2026-11-25 — GOV-WEBVIEW-010: control-plane producer-consumer trace

**Repository:** K41-Origin/k41-nexus-prototype  
**Source branch:** main — 56f354f2808ae5aba10b0e1d5892bd9fa7ca095b  
**Source tree:** 2d02bb29bd5a3aa8f74fc6a2ed21027149817e50; 856 entries, 739 blobs, truncated=false  
**Documentation branch before publication:** docs/hybrid-llm-engine-direction — 189e28970ec5d05abd536a57a4adab53399c27fd  
**Scope:** UI → transport → adapter/server ingress for every ClientMessage; server/adapter producer → recipient/fan-out → UI consumer for every ServerMessage.

### Governance reviewed before source inspection

- governance/README.md
- governance/AUDIT-REGISTER.md
- governance/AUDIT-METHOD.md
- governance/EVIDENCE-LOG.md
- governance/modules/webview-ui.md, server.md and adapters.md
- webview-ui/DEEP-AUDIT.md and FILE-REFERENCE.md
- webview-ui/PROTOCOL-MATRIX.md, TRANSPORT.md and STATE-MANAGEMENT.md
- Existing cross-module findings and continuation records were reconciled before assigning new IDs.

### Repository and source evidence

- Reconfirmed main at 56f354f2808ae5aba10b0e1d5892bd9fa7ca095b and complete recursive tree 2d02bb29bd5a3aa8f74fc6a2ed21027149817e50 (truncated=false).
- Reconfirmed docs branch head before publication at 189e28970ec5d05abd536a57a4adab53399c27fd.
- Webview source ledger entries for the selectively re-fetched producer/consumer files matched their current blob SHAs. Server (135 paths) and adapter (8 paths) remained reconciled to the same source baseline; no broad module restart was performed.
- Re-fetched relevant UI, adapter and server source files and recorded their blob SHAs in webview-ui/DEEP-AUDIT.md section 17.
- AsyncAPI remains the contract source of truth: 34 ClientMessage and 46 ServerMessage variants. All 80 variants now have static route/producer/consumer/delivery mapping in PROTOCOL-MATRIX.md sections 9–11.
- Corrected a stale route description: App emits focusAgent in both runtimes; the standalone server intentionally no-ops it because local drawer/tab focus is handled in App.

### Findings added

- WV-PROTO-01 — Area mapping mutation is persisted without notifying other connected clients. Static evidence shows sender-local optimistic update, no areaMappingsLoaded/store broadcast on save, handshake-only snapshot, and per-client OfficeState seat selection. Conditional multi-client divergence risk; Medium severity, High confidence, OPEN.
- WV-PROTO-02 — External asset directory reload is delivered only to the requesting client. The shared asset cache is mutated, but updated directory/sprite messages use the requesting socket callback; existing peers can retain stale UI asset state until reconnect. Low severity, High confidence, OPEN.
- Both findings are static, not runtime reproduced. Their multi-client effects remain conditional on multiple connected standalone clients and the intended consistency contract.
- WV-C08 and server F-08 remain the canonical schema-validation findings; this session adds examples and route mapping, not a duplicate validation finding. WV-APP-01, WV-RESYNC-01, WV-TR-01 and adapter A-01–A-10 remain unchanged.

### Documentation published

1. webview-ui/PROTOCOL-MATRIX.md — complete ClientMessage producer/ingress map, ServerMessage producer/recipient map, validation/authorization/fan-out summary, correction for focusAgent, and WV-PROTO-01/02. Blob SHA after final wording correction: 9a9d23521fecca560553929ab3ac37ba35baa51c.
2. webview-ui/DEEP-AUDIT.md — section 17 with source baseline, selected source blob SHAs, full trace result, findings, verification limits and continuation. Blob SHA after final wording correction: 0c9d5b7764f4f39bbafb44d7f9027ea020f0fba8.
3. governance/modules/webview-ui.md — session range through GOV-WEBVIEW-010, consolidated trace status, new finding references and revised continuation. Blob SHA: 11d2c5a87121d975a4232051dcfb830cc33e885a.
4. governance/AUDIT-REGISTER.md — consolidated webview status and continuation updated; stale docs-branch pre-publication pointer corrected to 189e28970ec5d05abd536a57a4adab53399c27fd. Blob SHA after explicit finding-ID wording: 69cf7be2db0c5ca17c0f2b5df76ed62bf6bac9d9.
5. This EVIDENCE-LOG.md entry is append-only.

### Verification limits

- SOURCE-VERIFIED / TREE-VERIFIED / DOC-RECORDED only. Static routing does not prove runtime delivery or multi-client consistency.
- No tests, build, lint, typecheck, protocol generation, browser/VS Code runtime, multi-client WebSocket, PTY or reconnect experiment was executed.
- No application source, tests, dependencies, package manifests, assets or configuration were modified.
- Documentation changes were limited to the five owning audit documents above; no source implementation change was made.

### Next continuation point

Complete the field-level validation/authorization and side-effect matrix using WV-C08 and F-08 as canonical findings. Confirm intended multi-client semantics for WV-PROTO-01/02 before proposing implementation changes. Then continue targeted lifecycle/disposal/reconnect analysis. Runtime verification remains unauthorized and OPEN.


## 2026-11-25 — GOV-WEBVIEW-011: field-level validation, authorization, persistence and side-effect audit

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch/ref:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Documentation branch:** `docs/hybrid-llm-engine-direction`  
**Scope:** all 34 ClientMessage variants; cross-layer static trace through AsyncAPI contract, standalone WebSocket ingress/handler, VS Code webview dispatcher, shared persistence helpers, and response/fan-out behavior. Existing 34/34 + 46/46 producer-consumer map reused, not repeated.

### Governance and historical coverage

Read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, module records for webview-ui/server/adapters, and webview DEEP-AUDIT, FILE-REFERENCE, PROTOCOL-MATRIX, TRANSPORT and STATE-MANAGEMENT before source inspection. Historical status: all 459 webview paths reconciled; 261 source rows REVIEWED; 54 tests TEST-INVENTORIED; 34 ClientMessage and 46 ServerMessage routes traced in GOV-WEBVIEW-010; canonical validation gap WV-C08 / F-08; multi-client findings WV-PROTO-01/02.

### Repository/source verification

- Source ref fetched: `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; fetch_commit confirms the referenced commit exists.
- A fresh recursive tree could not be fetched with the available GitHub connector actions in this session. The 856-entry / 739-blob / truncated=false source tree and prior full SHA reconciliation are therefore DOC-RECORDED from GOV-WEBVIEW-008, not newly TREE-VERIFIED here.
- Selected source blobs fetched from main: `core/asyncapi.yaml` ff1247118819fe68852da11996c660df1726aa66; `server/src/clientMessageHandler.ts` d7ae8235ee75de63b1716a4b67f27d69023f5d96; `server/src/httpServer.ts` ad5104a14233aa29834e008833a0f21458fc887b; `server/src/configPersistence.ts` 056e71dec89dd174df80d65da349cfafff57a5b7; `server/src/directories.ts` a27617b843733ff8eb5a6a0263324587bb57a32b; `server/src/characterProfilesPersistence.ts` 6444df2a8a032f0023426a226244d25cd7178a85; `server/src/cardOrder.ts` 3c4d7bd9397546dfe01081593045a9edaa57a0fa; `server/src/conversations.ts` bb698e3ce55862309d53320841550556d0d90b7f; `server/src/terminal/standaloneAgentLauncher.ts` f17562fcf673e5b8db4a7bd94d9ff953c4edf0b4; `adapters/vscode/PixelAgentsViewProvider.ts` 94d1ab2e1427377e67ff430a852d49863e5e4d87.

### Results

- 34/34 ClientMessage variants classified for contract fields, runtime validation, authorization/read-only gate, persistence/side effects and acknowledgements/fan-out. Detailed matrix: PROTOCOL-MATRIX.md §14–16.
- Standalone's connection-level privileged gate is fail-closed; it does not substitute for field-level validation. VS Code dispatches webview objects without a complete runtime schema validator.
- Stronger validation exists for profiles/wardrobe, Directories, card order, conversation query/session UUID and consent choice. Weak/absent checks remain for seats, layout/import nested shape, area mappings, general settings toggles, setHooksEnabled.enabled and external asset paths.
- Durable writes are split across shared config, layout, character/wardrobe files, adapter state and conversation looks; a number of write paths have no negative acknowledgement. Character + wardrobe writes are atomic individually, not a cross-file transaction.
- Existing WV-C08 / F-08 extended, not duplicated. New WV-PROTO-03 records external asset directory changes reporting the in-memory candidate even when writeConfig returns false (Low, High confidence, OPEN, static only). WV-PROTO-01/02 remain open.

### Verification limits

Static only. No tests, build, lint, typecheck, protocol generation, runtime/browser/VS Code, malformed payload, write-failure fault injection or concurrent-client experiment. No application source modified. The exact runtime symptom of WV-PROTO-03 and malformed nested payload effects remain OPEN.

### Documentation changes

Appended detailed field-level matrix and findings to webview PROTOCOL-MATRIX / DEEP-AUDIT; updated webview governance module, consolidated register and this append-only evidence log. Publication must be verified by re-fetching every changed document and recording the resulting branch head/blob SHAs.


## 2026-11-25 — GOV-WEBVIEW-012: cross-layer lifecycle, reconnect and state reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `main`  
**Source commit:** `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Documentation branch:** `docs/hybrid-llm-engine-direction`  
**Documentation branch tip before this session's publication:** `08ee7fc7edddc358ba883cb4d25d6282becd10a8`  
**Scope:** standalone control-plane reconnect and snapshot replay; VS Code webview readiness/buffering/restore; per-agent terminal data-plane reconnect; UI/OfficeState reconciliation and cross-layer lifecycle ownership.

### Governance and historical work reused

Read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, module records webview-ui/server/adapters, webview DEEP-AUDIT, FILE-REFERENCE, PROTOCOL-MATRIX, TRANSPORT and STATE-MANAGEMENT. Existing coverage reused: 34/34 ClientMessage + 46/46 ServerMessage route map; 34/34 field-level validation/authorization/persistence/side-effect matrix; server 135/135 audit; adapter 8/8 source audit. Existing findings preserved: WV-APP-01, WV-RESYNC-01, WV-TR-01, WV-PROTO-01/02/03, A-03/A-05/A-06 and F-01/F-03/F-05.

### Fresh source tree and SHA reconciliation

- Retrieved Git Trees API recursively for `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`: tree SHA `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`; 856 entries, 739 blobs, 117 trees, `truncated=false`.
- Webview FILE-REFERENCE: parsed 459 ledger path/blob-SHA rows and compared against all 459 `webview-ui/` tree blobs: 459/459 match, 0 missing, 0 extra, 0 mismatched.
- Adapter FILE-REFERENCE: parsed 8 current blob-SHA rows and compared against all 8 `adapters/` tree blobs: 8/8 match, 0 missing, 0 extra, 0 mismatched.
- Server FILE-REFERENCE contains 135 path/responsibility rows but no per-file SHA column/manifest. The server governance record documents historical 135/135 SHA reconciliation at this same source commit. Current tree contains 135 server blobs; all selected server source files below were compared directly to current tree SHAs. No claim is made that all 135 server SHAs were independently compared to a standalone ledger in this session.
- Documentation branch head fetched before publication: `08ee7fc7edddc358ba883cb4d25d6282becd10a8`.

### Selected source evidence

All selected SHAs below match the fresh recursive tree at the source commit:

- `webview-ui/src/hooks/useExtensionMessages.ts` — `649025df90edef84d24c40556cee2ea007e15ccf` (reconnect resync cleanup lines 305–325; existingAgents reconciliation 469–509; ready/reconnect subscription 910–923).
- `webview-ui/src/transport/webSocketTransport.ts` — `33008b95327f9e2a18e23aaf0f2060416c95aba9` (queue lines 24, 60–70, 93–99; message dispatch lines 72–78).
- `webview-ui/src/transport/onReconnect.ts` — `030ae419587d5f3b69b78d74cf295811087e2bcd`.
- `webview-ui/src/transport/postMessageTransport.ts` — `f9b622f6bc28eb18b3bb7131e10c596ad7e68acd`.
- `webview-ui/src/transport/reconnectBackoff.ts` — `44dc5aac6b52a73f35700509036a248396e3cc20`.
- `webview-ui/src/App.tsx` — `3eb91a2050da5f789223bc0a045a80fc841d60cb` (dirty callback lines 89–92; useExtensionMessages wiring lines 94+).
- `webview-ui/src/office/engine/existingAgents.ts` — `f23ff3f79f5fc6baf37f2915f8ff6abde107ce35` (restore/reconcile lines 55–100).
- `webview-ui/src/office/engine/officeState.ts` — `1afd778f4c72f1901ef91266e29a89f117e180c7` (addAgent idempotency guard lines 453–468; rebuild/sync path was inspected in targeted ranges).
- `webview-ui/src/terminal/terminalClient.ts` — `55336df6e5d593008c0f1c7d34c8d0d0c5ae3d10`.
- `webview-ui/src/hooks/useTerminalDrawer.ts` — `2913c2331ad4bee5f127a249502a394bfb414980`.
- `server/src/clientMessageHandler.ts` — `d7ae8235ee75de63b1716a4b67f27d69023f5d96` (handshake snapshot ordering lines 700–770).
- `server/src/httpServer.ts` — `ad5104a14233aa29834e008833a0f21458fc887b` (connection and store subscription lifecycle lines 180–280).
- `server/src/agentStateStore.ts` — `e73e5e47550cdfb1560ecdd0514b75be3688c5c1` (agentAdded/agentRemoved emission lines 115–128).
- `server/src/agentMessages.ts` — `df521f7a6d9ddbad8d7ee0c567df4054c500b9ea` (agentCreated payload includes palette/repeat/seatId lines 8–22).
- `server/src/agentActivityResend.ts` — `f0c6fe190f9d259c6c172cfd7a4198ecc64dfb77` (activity replay lines 14–95).
- `adapters/vscode/PixelAgentsViewProvider.ts` — `94d1ab2e1427377e67ff430a852d49863e5e4d87` (webviewReady restore lines 675–806; async layout and existing snapshot send lines 975–982).
- `adapters/vscode/agentManager.ts` — `30020b3e34d2d83fa4536e0d27f55c9e4fe3295c` (store.set on restore lines 362–370; snapshot metadata from adapter.loadSeats lines 495–531).

### Lifecycle map and findings

- Standalone control plane: WebSocket reconnect → onReconnect → webviewReady → server sends settings/assets/mappings/directories → existingAgents → layoutLoaded → activity replay → terminalSessionOpened. Client snapshot resync removes stale agents and resets transient tool/status/subagent state. Terminal sockets are separate, reconnect per agent, replay scrollback, and reassert geometry; terminal input is not buffered while disconnected.
- VS Code: PostMessageTransport has no reconnect state. Each fresh iframe resets readiness and its pending buffer; webviewReady flushes buffered events, restores agents, then async asset/layout work completes. Store restore emits agentAdded before sendExistingAgents; webview immediately materializes agentCreated before layout is ready; existingAgents metadata is buffered, and later restoreAgent's addAgent is a no-op for an existing ID. This extends WV-RESYNC-01 with a conditional initial-hydration path where separate persisted seat/look metadata may not override earlier agentCreated values.
- **WV-RESYNC-02** added: every ClientMessage sent while WebSocket is not OPEN is queued and replayed on next open without command classification, expiry, coalescing, acknowledgement or cancellation. Delayed launch/resume/close and stale settings writes are possible. Severity Medium, confidence High, OPEN, static only.
- Existing **WV-APP-01**, **WV-RESYNC-01**, **WV-TR-01**, **WV-PROTO-01/02/03**, adapter **A-03/A-05/A-06**, server **F-01/F-03/F-05** remain open. No duplicate finding created for the VS Code hydration extension.

### Verification limits

Static only. No tests, builds, lint, typecheck, protocol generation, browser/VS Code runtime, WebSocket drop/reconnect, PTY attach, multi-client test or fault injection was executed. No application source/test/assets/config were modified. Runtime reachability and visible impact of both WV-RESYNC-01 trigger paths and WV-RESYNC-02 remain OPEN.

### Documentation changes

Updated webview DEEP-AUDIT, TRANSPORT, STATE-MANAGEMENT, webview governance module, adapter/server governance cross-links, consolidated AUDIT-REGISTER and appended this evidence log. Re-fetch verification and resulting docs branch head/blob SHAs must be recorded after publication.


### Post-publication documentation verification — GOV-WEBVIEW-012

After the initial publication, all eight changed documents were fetched again from `docs/hybrid-llm-engine-direction`. Every fetched blob SHA matched the SHA returned by its corresponding update operation, and the expected GOV-WEBVIEW-012/WV-RESYNC-02 markers were present:

- `webview-ui/DEEP-AUDIT.md` — `968ba59b7adcbdebdb8241aa933b17d546a2eb44` (corrected tree SHA wording below in this evidence entry's follow-up publication).
- `webview-ui/TRANSPORT.md` — `108b52814f05b4503aa77d0c732642189017f0f0`.
- `webview-ui/STATE-MANAGEMENT.md` — `6a14bfa2d754cc4ffc71d0ece9f6146e6c313a7b`.
- `governance/modules/webview-ui.md` — `67389f0e740a239d5932134100a5f3a2d19f10ac`.
- `governance/modules/adapters.md` — `75d000e9c137b291fc7a823474577f4c112e0cfe`.
- `governance/modules/server.md` — `ecb330b538610e24d9cb823c6e08019a849e72e9`.
- `governance/AUDIT-REGISTER.md` — `009a3c96dc5957f62b3520c720b19336b0b7e6d7`.
- `governance/EVIDENCE-LOG.md` — `544b8b262d70b78c01820d4ccac641e6d840f920`.

The branch head immediately after that publication was `2f95e172eeacadd4abe0eebeaef5666359f75848`. A follow-up correction records the canonical recursive tree object SHA as `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (the commit SHA remains `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`). The corrected DEEP-AUDIT and EVIDENCE-LOG files are re-fetched once more after this correction.


## 2026-11-25 — GOV-E2E-001: E2E inventory, SHA reconciliation and initial static audit

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`; 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@cbf2d7ed7f711bc44fb619e43d86c560e1cf7d7e`.  
**Documentation branch before evidence-log append:** `4b56416e7d4aa2640fcec4745b130c7eadaa047a`; tree `54baced42554453f8dbc0eb7fd47665a8182b77c`.  
**Scope:** complete `e2e/` tree inventory, path/blob-SHA reconciliation, individual blob retrieval, E2E README/test inventory, harness architecture and initial static process/port review.  
**Exclusions:** scripts implementation, CI workflows, test/build/lint/typecheck execution and runtime experiments.

### Governance and historical coverage

Read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, module records for core/server/webview-ui/adapters, and the existing E2E README and FILE-REFERENCE template before source inspection. E2E had no prior governance module record or deep-audit document; its prior state in the register was INVENTORY / SAMPLING PENDING. Existing core/server/adapter/webview findings were reused by ID, not duplicated.

### Repository and inventory evidence

- Retrieved the complete recursive Git tree for `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; `truncated=false`.
- `e2e/` contains **47 blobs** and **7 tree directories**.
- All 47 blobs were fetched individually. The subtree includes one suite README, six fixture/shim files, global setup, 15 helpers, Playwright/run/TypeScript configuration, and 21 spec files.
- Retrieved the complete recursive tree at the documentation branch's tree object `1883eeea8af8fc8217d7d279be6ab34ab4d91bb6`; `truncated=false`.
- Compared every E2E `(path, blob SHA)` pair: source branch 47, documentation branch 47, **0 added / 0 removed / 0 changed**.
- The exact path, mode, byte size and SHA manifest is maintained in `docs/current-architecture/e2e/FILE-REFERENCE.md`; it is not duplicated in this log.

### Test inventory evidence

- Fetched all 21 spec files.
- Structural source count: **152 test declarations** and **152 @area tags**; this agrees with the generated test inventory in `e2e/README.md`.
- Counted **8 platform skip guards**, separately from test declarations.
- This is structural/test inventory evidence, not a full assertion-level review of every spec and not test execution.

### Initial static findings

- **E2E-001 — standalone process termination fallback does not reliably escalate after SIGTERM.** Evidence: `e2e/helpers/standalone.ts:188-201`, SHA `ab94998af9c08316a126ef1c2b0ca8f8c269cb17`. Node's `ChildProcess.killed` indicates signal delivery, not process exit; after SIGTERM the fallback condition can therefore be false while `exitCode` remains null. Related teardown helper `killTrackedExternalProcesses` sends SIGTERM and clears its registry without awaiting process exit (`e2e/helpers/mock-claude.ts:325-340`, SHA `90754501c25ee2b7af4720b00646b557eade48e9`). Medium severity, High confidence, STATIC-FINDING, OPEN; no runtime reproduction.
- **E2E-002 — standalone port-0 allocation has a close-before-bind race.** Evidence: `e2e/helpers/standalone.ts:84-101`, SHA `ab94998af9c08316a126ef1c2b0ca8f8c269cb17`. The temporary listener closes before the CLI binds the returned port; no reservation spans the handoff and no retry is visible. Low severity, High confidence, STATIC-FINDING, OPEN; no collision was induced.

### Cross-module consolidation

Preserved canonical findings and linked E2E coverage gaps to their owners:
- server F-01/F-03/F-05 for lifecycle ownership, unregister and shutdown;
- adapters A-03/A-05/A-06 for restore/removal, listener disposal and grace timer;
- webview WV-RESYNC-01/02, WV-APP-01, WV-TR-01 and WV-PROTO-01/02/03 for reconnect/snapshot, queued commands and shared-state fan-out;
- server F-08 / webview WV-C08 for runtime validation.
No existing finding ID was reused or duplicated.

### Documentation published

1. `docs/current-architecture/e2e/DEEP-AUDIT.md` — initial technical audit, E2E-001/E2E-002 and cross-module map; verified blob SHA `b22f92b95da231ba9c1cdab9646ce46679aba070`.
2. `docs/current-architecture/e2e/FILE-REFERENCE.md` — 47-row path/mode/size/SHA ledger; verified blob SHA `45e0cb2a32bc7b2024ae5b6a84f0443b05e2e4a3`.
3. `docs/current-architecture/e2e/README.md` — audit navigation and status; verified blob SHA `27578f34c9dab8d7e1d5de830805fc8fa94995a9`.
4. `docs/current-architecture/governance/modules/e2e.md` — E2E module ownership/status; verified blob SHA `33308c1343741a7170dc0ef9720b6bcf4ac6ad12`.
5. `docs/current-architecture/governance/README.md` — E2E record and documents integrated into governance index; verified blob SHA `014c0570dc167aa57d4a4c3d285dce1fc301a1da`.
6. `docs/current-architecture/governance/AUDIT-REGISTER.md` — E2E status and next action consolidated; verified blob SHA `3debabc05d7171d422a9ddf50be676b570d8b2ca`.

### Verification limits

- TREE-VERIFIED / SHA-RECONCILED / FETCHED for all 47 E2E blobs.
- Structural test inventory for 21 specs / 152 declarations; assertion-level review remains IN PROGRESS.
- E2E-001 and E2E-002 are static findings only.
- No tests, builds, lint, typecheck, install, browser/VS Code host, PTY, hook fault injection or runtime experiments were executed.
- No application source, tests, package manifest, dependencies or configuration were modified.

### Next continuation point

Complete assertion-level review of all 21 spec files and full review of the harness/helper/config files. Then trace only the named E2E contracts into the owning source modules. Inspect `scripts/run-e2e.mjs`, `scripts/generate-e2e-inventory.mjs`, report generation and CI workflows under the scripts and `.github/` governance scopes. Runtime verification remains unauthorized and OPEN.


## 2026-11-25 — GOV-E2E-002: Assertion-level review of all E2E specs

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`; recursive tree `truncated=false`, 856 entries.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@b573e28d0c6027c68679bd826c0097fec47db37e`.  
**Source reconciliation:** 21/21 spec blob SHAs match the E2E file ledger; no changed test blobs.  
**Scope:** assertion-level review of all 21 `e2e/tests/**/*.spec.ts` files, including test setup, assertions, state-hook reads, waits/polls, persistence checks, negative assertions and UI gesture boundaries.  
**Exclusions:** helper/fixture/config full review, application-source re-audit, scripts/CI, and all test/build/lint/typecheck/runtime execution.

### Governance and continuation

Re-read governance README, audit register, audit method, E2E module record, E2E deep audit, E2E file ledger and the latest evidence-log tail before continuing. The active source commit remains `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; the documentation branch is tracked separately. Existing E2E-001 and E2E-002 remain open and unchanged.

### Assertion-level coverage

- **21/21** spec files reviewed at assertion level.
- **152/152** executable `test()` declarations carry `@area:` tags and match the generated inventory in `e2e/README.md`.
- **8** platform skip guards are separate from the 152 test cases.
- Reviewed assertion intent and boundaries across Areas, carpet, editor, launch drawer, lifecycle, matrix, pets, consent, teams, characters, conversations, Directory modal, standalone hooks, mobile, multi-server hooks, terminal and standalone UI.
- All 21 test spec file rows were updated to `TEST-INVENTORIED` in `FILE-REFERENCE.md`.
- This is test source/assertion review only. **No test was executed.**

### New static test-quality findings

- **E2E-003 — seeded-layout Areas gate assertion is not isolated.** Evidence: `e2e/tests/claude/hooks-off/areas.spec.ts:92-103`, SHA `4b6b96fdee7634361bdc0b754c13038fb07bcf1d`. The workspace Directory also satisfies the production OR gate, so button visibility does not prove the seeded-layout branch. Low, High confidence, STATIC-FINDING / TEST-INVENTORIED, OPEN.
- **E2E-004 — carpet/furniture coexistence assertion does not prove co-location.** Evidence: `e2e/tests/claude/hooks-off/carpet.spec.ts:297-318`, SHA `668eff0627a55adaf1682c3650c088321ba7fa81`. The test asserts carpet at (3,3) and furniture count >= 1, but not furniture coordinates. Low, High confidence, STATIC-FINDING / TEST-INVENTORIED, OPEN.
- **E2E-005 — mobile hooks-info modal test omits vertical viewport bounds.** Evidence: `e2e/tests/standalone/mobile.spec.ts:417-439`, SHA `bc2cb73f0f7e63850bc2edd61a82ec2c6034245b`. It checks horizontal x bounds only; vertical clipping can pass. Low, High confidence, STATIC-FINDING / TEST-INVENTORIED, OPEN.
- **E2E-006 — pet persistence test checks count, not identity/type.** Evidence: `e2e/tests/claude/hooks-off/pets.spec.ts:147-197`, SHA `37cc965cf3e73bea101553e68d325419704ff17e`. Disk and reloaded arrays are checked for length two, not pet type/identity round-trip. Low, High confidence, STATIC-FINDING / TEST-INVENTORIED, OPEN.

No existing findings were duplicated. E2E-001/E2E-002 remain the process teardown and port allocation findings.

### Documentation updated

1. `docs/current-architecture/e2e/DEEP-AUDIT.md` — assertion coverage matrix and E2E-003–E2E-006; blob SHA `c5ddb685cd073f36cfe4257401b546f7e37535f4`.
2. `docs/current-architecture/e2e/FILE-REFERENCE.md` — all 21 spec rows marked TEST-INVENTORIED; blob SHA `6028da89681c021b150a9ada8414eba1f4c66036`.
3. `docs/current-architecture/e2e/README.md` — status and findings pointer; blob SHA `56b3d7b6729f2ac82f7110728c209d293bb27a8c`.
4. `docs/current-architecture/governance/modules/e2e.md` — module status and finding summary; blob SHA `16188e678baa5d366286092b8dee9d9761e858c3`.
5. `docs/current-architecture/governance/AUDIT-REGISTER.md` — consolidated E2E status and next gap; blob SHA `fd716b4c1b864e19da95eb3527ba63569ab977fa`.

### Verification boundary and next action

- **TREE-VERIFIED / SHA-RECONCILED:** source tree and all E2E paths remain unchanged at the recorded source commit.
- **TEST-INVENTORIED:** 21/21 spec files, 152/152 test cases.
- **STATIC-FINDING:** E2E-003–E2E-006; all remain OPEN.
- **TEST-EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.
- No application source, test code, package manifest, dependency or configuration was modified.

Next: finish static source review of the remaining E2E fixtures/helpers/configuration, tracing scenario scheduling, process cleanup, temporary directories, artifacts and Playwright orchestration. Then move to `scripts/` and `.github/` under their own audit scopes. Runtime verification remains unauthorized.


## 2026-11-25 — GOV-E2E-003: full static source review of fixtures, helpers, mock processes and E2E configuration

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree baseline:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated=false`; historical complete-tree snapshot).  
**Documentation branch:** `docs/hybrid-llm-engine-direction`; pre-publication tip recorded by GOV-E2E-002 as `8a1cb6399d51e5ecb15806c5d4b0750d0535cc01`.  
**Scope:** all six fixture/shim files, all 15 helper files, `global-setup.ts`, `playwright.config.ts`, `run-config.ts` and `tsconfig.json`. Test specs were not re-reviewed.  
**Exclusions:** `scripts/`, `.github/`, app-source re-audit, tests/build/lint/typecheck/install/runtime execution.

### Governance and continuity

Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, E2E module record, E2E DEEP-AUDIT and FILE-REFERENCE. Preserved GOV-E2E-001 inventory and GOV-E2E-002 assertion review (47/47 blobs; 21/21 specs; 152/152 tests). The source branch remains at the recorded main commit; the docs branch is a separate documentation stream. All 25 non-spec harness files were individually re-fetched and their current blob SHAs match the existing FILE-REFERENCE ledger. Full-file line counts and SHA identities are recorded in FILE-REFERENCE under GOV-E2E-003.

### Review coverage

- **Fixtures/shims:** 6/6 — POSIX/Windows mock CLI wrappers, mock scenario runner, VS Code fixture, standalone fixture, narration tail.
- **Helpers:** 15/15 — Allure labels, editor, external monitor, hook client, internal agent, intro, VS Code launch, layout seed, lifecycle/team, mock process/scenario manager, office, standalone host, transcript/team utilities, narration, webview.
- **Setup/config:** 4/4 — global setup, Playwright configuration, run-ID namespace helper, strict TypeScript config.
- **Total:** 25/25 full-file static source review. All 21 test specs retain their prior TEST-INVENTORIED status; no test was executed.

### Findings

- **E2E-007 — run ID `..` escapes the artifact namespace and broadens cleanup target.** Evidence: `e2e/run-config.ts:3-19`, SHA `1921baad5195765b91a6661afd2f75415c49be96`; cleanup sink `e2e/global-setup.ts:7,136-137`, SHA `bb0b9611947b1ebaccea2cdd186811d2ab898176`. Sanitization allows dots and does not reject the special `..` segment; `path.join(baseDir, '..')` resolves to the parent, and global setup recursively removes the resulting Allure path. Medium, High confidence, STATIC-FINDING, OPEN. No deletion/runtime experiment performed.
- **E2E-008 — VS Code download lock has no stale-owner recovery.** Evidence: `e2e/global-setup.ts:24-72`, SHA `bb0b9611947b1ebaccea2cdd186811d2ab898176`. The lock writes PID/time but contention handling never checks owner liveness or expires stale locks; abnormal termination before `finally` can leave later setup waiting until its ten-minute timeout. Low, High confidence, STATIC-FINDING, OPEN. No lock fault injection performed.

Existing E2E-001–E2E-006 remain OPEN and unchanged. No existing ID was reused; the new findings are distinct from process teardown, port allocation and assertion-quality gaps.

### Documentation publication and verification

Updated E2E DEEP-AUDIT, FILE-REFERENCE, architecture README, governance module record, consolidated AUDIT-REGISTER and appended this evidence entry. After publication, all six documents were fetched again and their blob SHAs/content markers checked. The audit branch is the only write target.

### Execution and modification limits

- Static/read-only repository investigation plus audit-document publication only.
- No test, build, lint, typecheck, install, browser, VS Code host, PTY, process fault injection or runtime experiment executed.
- No application source, test, package manifest, dependency or configuration file modified.

### Next continuation

The full E2E harness static review is complete. Continue targeted cross-module source-contract tracing for lifecycle, hooks, reconnect, persistence and test-hook boundaries, reusing canonical server F-01/F-03/F-05/F-08, adapter A-03/A-05/A-06 and webview findings rather than duplicating them. Then audit `scripts/` and `.github/` under their own governance scopes. Runtime verification remains unauthorized.


## 2026-11-25 — GOV-E2E-004: targeted cross-module source-contract tracing

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@749094e910eccb112a7267435af9ff6bb71b6dd2`.  
**Governance reread:** governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG; E2E/server/adapters/webview module records and deep audits; server PERSISTENCE.md.  
**Scope:** lifecycle, hook execution, reconnect, persistence and E2E test-hook boundaries. Only files needed for these named contracts were re-fetched; prior 47/47 E2E inventory, 21/21 assertion review and 25/25 harness review were not restarted.

### Source contract trace

- **Lifecycle:** VS Code launch/restore/close paths were traced through `PixelAgentsViewProvider`, `agentManager`, `AgentRuntime`, `AgentStateStore` and `HookEventHandler`. Standalone launch/reattach was traced through `standaloneAgentLauncher`, PTY exit callbacks and CLI shutdown. Existing server F-01/F-03/F-05 and adapter A-03/A-05/A-06 remain canonical.
- **Hook execution:** mock runner settings-command matching and stdin invocation → installed `claude-hook.js` registry fan-out → authenticated `POST /api/hooks/:providerId` → runtime callback → provider normalization → SessionRouter → HookEventHandler → AgentStateStore/WebSocket was traced. Direct E2E `sendHookEvent()` tests bypass the installed hook script; scenario `emitHook` covers that separate process boundary. Preserve F-04: HookEventHandler receives but ignores providerId.
- **Reconnect:** standalone `WebSocketTransport` queues disconnected ClientMessages; state subscribers run on CONNECTED before the old queue flush, while `onReconnect` sends `webviewReady`. Server snapshot order is existingAgents → layoutLoaded → activity replay → terminal-session announcements. Preserve WV-RESYNC-01/02, WV-APP-01 and WV-TR-01. VS Code's postMessage surface is a fresh-iframe hydration, not a socket reconnect; the known initial seat metadata ordering extends WV-RESYNC-01.
- **Persistence:** AgentStateStore projects state through `toPersistedAgent` into namespace-specific FileStateAdapter files. Identity/look/team and selected background-spawn state persist; foreground active-tool/permission/turn state does not. Restore paths start JSONL watching at EOF; the existing initialReadOffset test explicitly rejects replaying old transcript history. Therefore in-process WebSocket reconnect can replay current activity, while process restart intentionally restores an agent at rest. Recorded as an explicit behavior boundary/design question, not a new defect. Existing PERSISTENCE.md already records cross-process read-modify-write lost-update risk; no duplicate finding created.
- **Test-hook boundary:** VS Code and standalone fixtures set the E2E flag with Playwright addInitScript before app initialization. App installs test hooks only when isE2E is true. Read hooks expose full pet/furniture state; E2E-004/E2E-006 assertions use only counts despite richer observability. selectAgent/petClick/editor action drivers bypass canvas pointer geometry; getTileCenter supports real gesture paths. These are test seams, not server authorization controls.

### New findings

- **Server F-09 — SessionEnd grace timer survives HookEventHandler disposal.** Evidence: `server/src/hookEventHandler.ts:385-398,793-796`, SHA `bd430b99f4abf7afea851645fe31924bf6213f43`; `server/src/agentRuntime.ts:595-623`, SHA `875efec343faada4763a2f4d75c336f9b625d54a`. A clear/resume SessionEnd creates an untracked timeout. Handler disposal only disposes SessionRouter; AgentRuntime.stop documents that it stops timers while preserving agents. If a host remains alive after stop, the old callback can still run against the preserved store. Low, High confidence, STATIC-FINDING, OPEN. Normal full-dispose and current CLI process-exit paths narrow the practical trigger. Existing `hookEventHandler.test.ts` dispose case does not schedule a grace timer before disposal.
- **E2E-009 — mock scenario queue claim is not atomic across concurrent processes.** Evidence: `e2e/fixtures/mock-claude-runner.cjs:39-63`, SHA `6072b23f637ec2cd3518965f0fabc97f230e9051`; `e2e/helpers/mock-claude.ts:278-284`, SHA `90754501c25ee2b7af4720b00646b557eade48e9`. Multiple child processes sharing one HOME can read the same queue head before rewriting the remaining array; no lock/CAS exists. Low, Medium confidence, STATIC-FINDING, OPEN. No concurrent-claim reproduction was performed.

### Existing findings preserved

- Server F-01/F-03/F-04/F-05/F-08 remain open/partially covered as previously recorded; F-09 is new.
- Adapter A-03/A-05/A-06 remain open.
- Webview WV-RESYNC-01/02, WV-APP-01, WV-TR-01, WV-PROTO-01/02/03 and WV-C08 remain open.
- E2E-001–E2E-008 remain open and unchanged; E2E-009 is new. No existing finding ID was reused.

### Documentation publication

Updated server DEEP-AUDIT and server module record; E2E DEEP-AUDIT, E2E module record and E2E README; consolidated AUDIT-REGISTER; appended this EVIDENCE-LOG entry. All seven changed documents were re-fetched after publication; blob SHA and required F-09/E2E-009/GOV-E2E-004/status markers were checked. Verified pre-final-log-update blob SHAs: server DEEP-AUDIT `22adda23b9f000abeb752d0794bca9db36494245`; server module `b3f52d852cd4fadb690ebf8e0b5331c7a3106f56`; E2E DEEP-AUDIT `83be5b0513115d9b140aa8c3015d3ed3c7220a3f`; E2E module `b616128a3a7beac7cffff2bdc1bd5908d6101852`; E2E README `625e437b192598605857bb6c7a18f3f1bf1e39b5`; AUDIT-REGISTER `45f4b034a7b1abeadd66a2bcdb5d0058344669f5`. The evidence log itself is re-fetched after this final append.

### Execution and modification limits

- Static source tracing and audit-document publication only.
- No tests, build, lint, typecheck, install, browser, VS Code, PTY, hook fault injection, reconnect/network interruption, multi-process stress or runtime experiment executed.
- No application source, tests, package/dependency/configuration files modified.

### Next continuation

Targeted cross-module source-contract tracing for lifecycle, hook execution, reconnect, persistence and test-hook boundaries is complete at static level. Next audit `scripts/` (E2E runner/inventory/report generation) and `.github/` workflows under their owning scopes. Runtime verification remains unauthorized.


## 2026-11-25 — GOV-SCRIPTS-001: complete static audit of scripts/

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@ed01e33e6be1914509518d6fe56a8fe5f5b2944d`.  
**Scope:** all tracked blobs directly beneath `scripts/`; `package.json` inspected for script-entry integration.  
**Governance reread:** governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, E2E module record, E2E DEEP-AUDIT and E2E FILE-REFERENCE.

### Inventory and reconciliation

- Complete recursive source tree contains 14 blobs directly under `scripts/`; all 14 were individually fetched and statically reviewed.
- Complete docs-branch tree at `ed01e33e6be1914509518d6fe56a8fe5f5b2944d` contains the same 14 paths and blob SHAs: 0 added, 0 removed, 0 changed.
- Complete per-file mode, size, line count, SHA and review state are in `docs/current-architecture/SCRIPTS-FILE-REFERENCE.md`.
- No file under `scripts/` was omitted.
- `scripts/npm-package-contract.test.mjs` contains 7 test declarations; assertions were inspected (`TEST-INVENTORIED`), not executed.

### Findings

- **BUILD-001:** JSONL viewer inserts untrusted tool names into HTML through event labels; Medium, High confidence, STATIC-FINDING, OPEN.
- **BUILD-002:** Asset Manager inserts a free-form group role into `innerHTML` without escaping; Medium, High confidence, STATIC-FINDING, OPEN.
- **BUILD-003:** imported group graph cycles are not validated before recursive traversal; Low, High confidence, STATIC-FINDING, OPEN.
- **BUILD-004:** discarded assets inside groups are included in grouped export; Medium, High confidence, STATIC-FINDING, OPEN.
- **BUILD-005:** `package.json` import-tileset command references absent `scripts/import-tileset-cli.ts`; Medium, High confidence, TREE-VERIFIED / SOURCE-VERIFIED, OPEN.
- **BUILD-006:** Allure failure branch calls `process.exit()` before staged-results `finally` cleanup; Low, High confidence, STATIC-FINDING, OPEN.
- **BUILD-007:** npm package smoke helper returns after SIGKILL without awaiting child exit; Low, Medium confidence, STATIC-FINDING, OPEN.

Related patterns in `verify-npm-package.mjs` are cross-referenced to E2E-001 (process termination) and E2E-002 (port-0 close-before-bind); no duplicate IDs were created. Existing E2E-001–E2E-009, server F-01–F-09, adapters A-01–A-10 and webview findings remain unchanged.

### Documentation publication

Created:
- `docs/current-architecture/SCRIPTS-README.md` — blob SHA `70018b8ca336fe8c2958e5ea050a243bfcb6ecc5`.
- `docs/current-architecture/SCRIPTS-FILE-REFERENCE.md` — blob SHA `e60def73771231c1ab17976363167b37ab136cd2`.
- `docs/current-architecture/governance/modules/scripts.md` — blob SHA `a3947bf8c2414f9d4530a595f7a32beed6b54219`.

Updated:
- `docs/current-architecture/SCRIPTS-AUDIT.md` — blob SHA `93c92a7b34638244a098782f4e352396e6a97b58`.
- `docs/current-architecture/governance/README.md` — blob SHA `a285909ce4ae049d46953697692a096bab37db36`.
- `docs/current-architecture/governance/AUDIT-REGISTER.md` — blob SHA `3a1f1be6efdbd7cf4ec1d46fdcd495643433a16d`.
- `docs/current-architecture/governance/modules/e2e.md` — blob SHA `feff055932c4faf0e46e9b7eab8fb5867ae49b7d`.
- `docs/current-architecture/e2e/DEEP-AUDIT.md` — blob SHA `e5fbe24bf2da77446053a66605bc52989f751b82`.
- `docs/current-architecture/e2e/README.md` — blob SHA `f5446029a48898a31c8fee2746c758932e9ac772`.

All changed documentation files, including this evidence log, must be fetched again after publication to verify the committed contents and status markers.

### Execution and modification limits

- Static/read-only repository investigation and audit-document publication only.
- No tests, build, lint, typecheck, generation, package, publish, browser, VS Code host or runtime experiment executed.
- No application source, scripts, package manifest, dependencies or configuration modified.

### Next continuation

The `scripts/` static audit is complete (14/14; BUILD-001–BUILD-007 OPEN). Continue with `.github/` workflow, permissions, triggers, secrets, artifact provenance and CI/release audit. Runtime verification remains unauthorized.


### GOV-SCRIPTS-001 publication SHA correction

The create-file API returned commit SHAs for newly created files, not content blob SHAs. After re-fetching the committed files, the verified **blob SHAs** are:
- `docs/current-architecture/SCRIPTS-README.md`: `2970c2ecb6d9863a555241788cba1cbdb7875480`.
- `docs/current-architecture/SCRIPTS-FILE-REFERENCE.md`: `7b48c76330f912543ffd36a0c200a5e8d7bdebf8`.
- `docs/current-architecture/governance/modules/scripts.md`: `e2676b8dbd9fdb1f11cccae48452651cc7efd3c8`.

The earlier three values in the GOV-SCRIPTS-001 publication list were create-file commit SHAs, not blob SHAs. This correction preserves the append-only history; the remaining listed values were verified blob SHAs. The evidence log is re-fetched after this correction.


## 2026-11-25 — GOV-GHCI-001: complete .github and CI/release static audit

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch/commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@7b56bcd278311bfdb782acacdd80080f74d7e87a`.  
**Governance reread:** governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, scripts module record/deep audit/file reference and E2E module record.  
**Scope:** all tracked `.github/` blobs, all four workflows, templates/Dependabot/funding, and targeted release integration paths in package.json and npm/VSIX packaging scripts.

### Inventory and reconciliation

- Complete source tree has 10 blobs beneath `.github/`; all 10 individually fetched and source-reviewed.
- Complete docs-branch tree at the pre-publication snapshot contains the same 10 .github paths/blob SHAs: 0 added, 0 removed, 0 changed.
- Four workflows reviewed in full: `ci.yml` (683 lines), `pr-title.yml` (33), `publish-extension.yml` (154), `update-badges.yml` (129).
- The three issue-template files, Dependabot config, PR template and funding file were also reviewed.
- Full file metadata and SHA evidence are in `docs/current-architecture/github-ci/FILE-REFERENCE.md`.

### Findings

- **BUILD-008 — same-repository PR-controlled code executes in Vercel-secret-bearing preview job.** `.github/workflows/ci.yml:500-518,550-585,608-626`; Vercel secrets are job-wide, fork check is the only trust filter, and PR-controlled npm lifecycle/report scripts run in that job. Checkout credential persistence plus job write permissions increase the exposed token surface. High, High confidence, STATIC-FINDING, OPEN.
- **BUILD-009 — workflow_dispatch can publish a VSIX from a ref not verified as main.** `.github/workflows/publish-extension.yml:3-11,29-35,65-81`; default dry_run is false, main ancestry check applies only to release event, marketplace publishing steps are unconditional. High, High confidence, STATIC-FINDING, OPEN.
- **BUILD-010 — Required Checks is not shown as enforced by repository-level branch policy.** CI documents the aggregate gate at `ci.yml:426-437`; branch metadata reports `protected:false`, repository rulesets endpoint returns an empty list. Medium, High confidence for repository metadata; organization policy remains unverified. OPEN.
- **BUILD-011 — third-party Actions references use mutable version tags, not full commit SHA pins.** Examples across all workflows are recorded in `github-ci/DEEP-AUDIT.md`. Medium, High confidence, STATIC-FINDING, OPEN.
- **BUILD-012 — update-badges workflow omits explicit GITHUB_TOKEN permissions.** `update-badges.yml:8-12,40-46,96-128`; effective rights inherit repository defaults. Low, High confidence that declaration is absent; actual effective rights unknown. OPEN.
- **BUILD-013 — VSIX release path does not compare release tag to package manifest version.** `publish-extension.yml:29-35,51-87` checks main ancestry only, whereas `scripts/npm-package-contract.mjs:130-136` enforces `v<manifest.version>` for npm. Medium, High confidence, STATIC-FINDING, OPEN.

### Workflow, artifact and repository metadata observations

- CI triggers on PRs to main and pushes to main; PR-title uses `pull_request_target` without checkout and only read permission; extension publishing triggers on published release and manual dispatch; badge update is scheduled and manually dispatchable; Dependabot runs weekly.
- npm publication verifies release identity and SHA-512 tarball integrity in the same job that generated the package. The publish command uses the local verified tarball rather than downloading a separate artifact.
- VSIX is smoke-verified and the same local output path is passed to Marketplace/Open VSX/release upload. No explicit VSIX checksum/attestation generation is present in the workflow.
- Allure artifacts are uploaded/downloaded within the same workflow run. No separate artifact attestation or explicit digest comparison is defined in the workflow YAML.
- The deploy precheck receives CI/E2E results but does not use them in its ready decision; its skip-summary branches for CI/E2E failure are unreachable in the current script. Whether report preview should be gated on failed tests remains an OPEN design-intent question, not a new finding.
- GitHub Actions workflow-runs API returned 0 runs and releases API returned an empty list. Branch metadata reports main `protected:false`; repository rulesets list is empty. Branch-protection detail endpoint returned 403 to the connected integration. Organization rulesets, repo Actions defaults, secret values/scopes and npm trusted-publisher settings were not available.

### Documentation publication and verification

Created:
- `docs/current-architecture/github-ci/README.md` — blob SHA `baaf0f2146a821a9afb6bc0c96394611a77798fa`.
- `docs/current-architecture/github-ci/FILE-REFERENCE.md` — blob SHA `781e9ab2d4cc4137b39760589fa0164fe208c356`.
- `docs/current-architecture/github-ci/DEEP-AUDIT.md` — blob SHA `b540b8db646ec9f63250533afefc4d7235f71f9c`.
- `docs/current-architecture/governance/modules/github-ci.md` — blob SHA `2d50c2ee1aede2ae71fe737601438abeaea2e47a`.

Updated:
- `docs/current-architecture/governance/README.md` — blob SHA `a9c34d3f6170ee37da1362006db7c91056b5f72e`.
- `docs/current-architecture/governance/AUDIT-REGISTER.md` — blob SHA `62a9e38fc0062330e3d2ac17a0e1f8c7ccb2de8c`.
- `docs/current-architecture/governance/modules/scripts.md` — blob SHA `0073a20a71ffa9f9c7cef3cbda881a924e1cc595`.

All seven changed audit documents were fetched again after publication and blob SHAs/status markers verified. This evidence log is appended below and must be re-fetched once more after the update.

### Execution and modification limits

- Static/read-only repository investigation and audit-document publication only.
- No workflow dispatch, CI execution, test, build, lint, typecheck, deployment, release, package publication or runtime experiment executed.
- No application source, workflow, package manifest, dependency or configuration modified.

### Next continuation

Resolve BUILD-008 and BUILD-009 trust boundaries first. Then verify organization-level branch rules, repository Actions default token permissions, secret scopes/environment protections, npm trusted publisher configuration and release artifact attestations when the required settings access is available. Preserve BUILD-001–BUILD-013 and all existing E2E/server/adapter/webview IDs.
## 2026-11-25 — GOV-GHCI-002: preview and release trust-boundary design review

Repository: K41-Origin/k41-nexus-prototype
Source branch/commit: main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b
Source tree: 2d02bb29bd5a3aa8f74fc6a2ed21027149817e50 — 856 entries, truncated=false.
Documentation branch before publication: docs/hybrid-llm-engine-direction@aa00fe9fd9a5dbf29b15b3ede1248fab2338bc7d.
Scope: targeted static continuation for BUILD-008/009 only; no repeat of the 10-file .github review.

### Governance and reconciliation

- Re-read governance README, audit register, audit method, evidence log, github-ci module record, deep audit and file reference.
- Re-fetched complete source and docs-branch recursive trees; both are truncated=false.
- Compared all 10 .github blob path/SHA pairs between source and docs branch: 10/10 identical; 0 added, 0 removed, 0 changed.
- Source and documentation branch tips were kept separate. No source blob change was found for the audited workflow paths.

### Additional source evidence

Eight integration files were individually fetched at the source snapshot and reviewed for trust-boundary contracts; exact sizes, line counts and blob SHAs are in github-ci/FILE-REFERENCE.md:
- package.json — 66efe28fd2927466c5f8283c6e26fe63da427079.
- server/package.json — f3e7e63e1920d6804eec70c5104914c436269351.
- webview-ui/package.json — 3729872b2736eaf6b66c608c1b40f4e24c25d2fb.
- scripts/assemble-vercel-output.mjs — 70391faa6cb171577bb02aff6e4a6e13963399ae.
- scripts/npm-package-contract.mjs — 329175a73e3bb2653dd7d2ada401492ecf3b5d4a.
- scripts/publish-npm-package.mjs — 7cfcf7bb2d64aa62dd5ed70f7de9a212af4bff82.
- scripts/verify-npm-package.mjs — 599b60cff9b10a22d06404d2b08e0c723baead15.
- scripts/verify-vsix-package.mjs — ed4d2e5476cfc6c7d3a9ec5865ecc3ee1e4f4803.

Workflow SHAs remain:
- .github/workflows/ci.yml — 79cbf4d0835327f67fec623b3b41651f41ed72f4.
- .github/workflows/publish-extension.yml — 713dc93fbd4a8f5d2c5f37cebdbf37587615205d.

### Trust-boundary conclusions

- BUILD-008: the fork-only precheck does not make same-repository PR code trusted. PR merge-ref content reaches a job with job-wide Vercel secrets, issue/PR write permissions, npm installation and PR-controlled report preparation. --prebuilt avoids a remote build but does not prevent local code from accessing job credentials. Required design: unprivileged PR build, exact run-scoped data-only artifact, trusted deployment workflow that never checks out or executes PR source, validated static output/config, Vercel credential only in deployment step, and separate comment permissions.
- BUILD-009: manual dispatch can select a branch/tag, the main ancestry guard is release-only, and dry_run defaults false while VSIX publish steps are unconditional. A non-main manual ref can therefore reach Marketplace/Open VSX publication. The npm job also grants job-wide id-token:write while selected-ref package code executes; GitHub confirms this permission enables OIDC token requests. Whether npm accepts a manual-run identity depends on trusted-publisher settings that were not accessible. Required design: dry-run-only unprivileged dispatch, release-tag publication after shared ref/commit/version preflight, isolated publisher jobs, protected Environment approval where manual publishing remains, and no OIDC during untrusted build/test execution.
- Checkout credential correction: actions/checkout@v7 stores the token-bearing Git config in a separate file under RUNNER_TEMP and references it from repo Git config. This corrects the earlier narrower claim that the token value itself is directly stored in .git/config; default credential persistence still leaves it available to same-job processes. External v7 source: action.yml SHA 5b0524f730db83f9513c18ab31a6c086c7239076; src/git-auth-helper.ts SHA dd7e6fbdb5387908190676a6ec9c4a828a044dea.
- BUILD-008 and BUILD-009 remain OPEN. Design review completion is not remediation or closure. BUILD-013 remains the owner of the separate VSIX tag/package-version mismatch.
- The .github/workflows/ci.yml tree size is 24,588 bytes; the previous FILE-REFERENCE value 24,566 was corrected. Blob SHA unchanged.

### External semantics consulted

- GitHub pull_request merge-ref and manual workflow-dispatch ref behavior.
- GitHub OIDC id-token:write permission and token request interface.
- npm trusted-publisher repository/workflow identity fields and optional environment.
- Vercel --prebuilt upload semantics.
- actions/checkout v7 credential-persistence implementation.

### Publication and verification

Updated github-ci/DEEP-AUDIT.md, github-ci/FILE-REFERENCE.md, github-ci/README.md, governance/modules/github-ci.md and governance/AUDIT-REGISTER.md; appended this session to the evidence log. Each changed file must be re-fetched after publication to verify committed contents and status markers.

### Execution and modification limits

- Static source inspection and audit-document publication only.
- No tests, build, lint, typecheck, workflow dispatch, OIDC request, package publication, release, deployment or runtime experiment executed.
- No application source, workflow, package manifest, dependency or configuration modified.

### Next continuation

The static design review for BUILD-008/009 is complete. Continue only with explicitly authorized remediation or verification of actual repository/org settings, secret scopes, Environment protections, npm trusted-publisher configuration and artifact provenance. Preserve BUILD-008–BUILD-013 and all other finding IDs.

## 2026-11-25 — GOV-DOCGOV-001: documentation governance reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Documentation branch:** `docs/hybrid-llm-engine-direction`  
**Documentation HEAD at session start:** `02b3118edf38713cc55b581a916fd61dea8bfd1b` (commit title: `docs(audit): record npm prepack OIDC execution edge`).  
**Source branch/commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`; 856 entries / 739 blobs / 117 trees; `truncated=false`.  
**Scope:** documentation-only governance reconciliation. No application source was changed.

### Branch reconciliation

- Retrieved the actual documentation branch ref; it pointed to `02b3118edf38713cc55b581a916fd61dea8bfd1b` before this session's documentation commits.
- Compared the complete recursive trees for that documentation snapshot and `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- All 739 source blob paths on `main` exist on the documentation branch with identical blob SHAs; 0 missing, 0 extra, 0 mismatched source blobs.
- The documentation branch has 61 additional documentation blobs. The two branch histories are not asserted to be equivalent.

### Governance discrepancies corrected

- Webview README now reflects GOV-WEBVIEW-005–012: 261 reviewed rows, 54 assertion-reviewed test files, completed 34/34 + 46/46 protocol trace and field-level audit; former 118 reported-review rows and inventory-only tests are no longer presented as open. Runtime, cross-client and font/binary verification remain open.
- Asset inventory now marks default layout and web manifest structural checks as complete while retaining semantic/runtime limitations; no full PNG decode or TTF internals are claimed.
- E2E module record and README continuation no longer ask to repeat GOV-E2E-004 cross-module tracing; they point to authorized runtime/test verification and open E2E findings.
- Legacy `scripts/README.md` and `scripts/FILE-REFERENCE.md` are explicitly navigation aliases to the canonical uppercase records, avoiding conflicting NOT AUDITED status.
- Scripts module cross-reference corrected from BUILD-008–BUILD-014 to BUILD-008–BUILD-013.
- GitHub CI module record now includes the latest npm `npm pack` → `prepack` execution path under `id-token:write` as additional evidence within existing BUILD-009; no new finding ID was created.
- Adapter dependency document now distinguishes completed 8/8 static adapter review from the incomplete exhaustive dependency/build-enforcement map.
- Infrastructure and CI/CD documents distinguish completed scoped `.github/` and scripts audits from the still incomplete integrated infrastructure audit.
- System dependency map now defines ownership, scope, edge evidence requirements and next action; its audit status remains NOT AUDITED.
- Governance README and register now link infrastructure and dependency-map ownership, record the actual documentation HEAD at session start, and distinguish source snapshot identity from runtime verification.

### Files updated

Module/technical navigation:
- `governance/modules/e2e.md`
- `governance/modules/scripts.md`
- `governance/modules/github-ci.md`
- `webview-ui/README.md`
- `webview-ui/ASSET-INVENTORY.md`
- `scripts/README.md` (legacy alias)
- `scripts/FILE-REFERENCE.md` (legacy alias)
- `adapters/DEPENDENCIES.md`
- `infrastructure/README.md`
- `infrastructure/CI-CD.md`
- `SYSTEM-DEPENDENCY-MAP.md`

Governance coordination:
- `governance/README.md`
- `governance/AUDIT-REGISTER.md`

### Execution and modification limits

- Documentation files only were modified.
- No application source, tests, workflows, package manifests, dependencies or runtime configuration were modified.
- No tests, build, lint, typecheck, generation, package, publish, CI dispatch, browser, VS Code host, OIDC request or runtime experiment was executed.
- BUILD-001–BUILD-013, F-01–F-09, A-01–A-10, WV findings, E2E-001–E2E-009 and core observations were not closed or renumbered.
- This log is append-only. Publication must be followed by re-fetching each changed file and recording the resulting committed blob SHA/branch HEAD verification in a subsequent evidence entry if a correction is needed.

### Remaining work after this session

1. Build the evidence-backed system dependency/lifecycle map.
2. Complete integrated infrastructure review for build, package, dependencies and test configuration; do not repeat scoped scripts or `.github/` reviews.
3. Perform authorized runtime/test verification for open findings.
4. Reconcile architecture direction and ADRs against implementation contracts and decide the 12 unresolved hybrid-engine architecture questions.
5. Verify actual organization/repository policy, Environment and secret scopes, npm trusted publisher and artifact provenance when access is available.


### GOV-DOCGOV-001 — publication completion and verification scope

The following additional records were aligned after the initial append above:
- `governance/modules/webview-ui.md`: latest continuation now points to authorized runtime/font evidence and explicitly marks GOV-WEBVIEW-011/012 static work complete.
- `e2e/README.md`: continuation no longer implies the completed GOV-E2E-004 trace remains outstanding.
- `governance/README.md`: HEAD label clarified as the documentation branch tip at session start, not the post-publication tip.

**Changed document set for post-publication verification:** governance README, AUDIT-REGISTER, EVIDENCE-LOG, module records for E2E/scripts/github-ci/webview-ui, webview README and ASSET-INVENTORY, E2E README, legacy scripts README/FILE-REFERENCE aliases, adapters DEPENDENCIES, infrastructure README/CI-CD, and SYSTEM-DEPENDENCY-MAP.

The current branch tip and each changed file's resulting blob SHA must be fetched after this append. This entry records the intended verification scope; it does not claim the post-publication verification until that fetch has occurred.


### GOV-DOCGOV-001 — post-publication verification completed

The documentation branch ref was re-fetched after all reconciliation updates and pointed to `3461ee250c654e08beb3083a90546e68ebc48a62`. All changed files in the session were then re-fetched from the branch; the committed contents and resulting blob SHAs are:

| Path | Verified blob SHA |
|---|---|
| `governance/README.md` | `d1ed12d4b36ed390aa145d5f749bd8615bc74ba6` |
| `governance/AUDIT-REGISTER.md` | `896dc0d7c6c04ef6d6efba294f0b96c5ddb0ca77` |
| `governance/EVIDENCE-LOG.md` (pre-verification append) | `ce7c9b9a6f23a5c164ab8b26025997e530a4d7ef` |
| `governance/modules/e2e.md` | `b38933d02bc8203b16ec378dea1317f0b4f1327e` |
| `governance/modules/scripts.md` | `e727df47c8d728e80051769b86932e91d9bee193` |
| `governance/modules/github-ci.md` | `808a6eaca2c82b844f464563fd809f96bd9b5897` |
| `governance/modules/webview-ui.md` | `40dbf69e53008f12900e1865398ae0ed4bd5fea9` |
| `webview-ui/README.md` | `b75ab9d4e4578de3842bc8cea5b78392cfa8f49a` |
| `webview-ui/ASSET-INVENTORY.md` | `e9240843a5093ce2492c679d0a113123528235c9` |
| `e2e/README.md` | `2d1795fa25279bc2be192659d8eb5fc5408459d4` |
| `scripts/README.md` (legacy alias) | `5939b790a500f430a4f3f2ecb9a0c12c017933a5` |
| `scripts/FILE-REFERENCE.md` (legacy alias) | `6166eb69f4ea23949e5a5fe502cb9afcd0c42414` |
| `adapters/DEPENDENCIES.md` | `6743be44249dfe8efed605fd42eb70ee4b8db1de` |
| `infrastructure/README.md` | `45b913842d891231cdcc0d953d3aa6f8ba559594` |
| `infrastructure/CI-CD.md` | `ba31ce7d767f71e2a55f65a69a394074d1ef6c2b` |
| `SYSTEM-DEPENDENCY-MAP.md` | `0382c1e79f237d26ddffe06e1ea2b0d2752ca16a` |

All 16 paths returned the expected updated content and blob SHA. The evidence-log blob listed above is the version fetched before this final verification append; this current entry must itself be fetched once after commit to verify its final blob SHA. No application source changed.


## 2026-11-25 — GOV-GHCI-003: BUILD-008/009 remediation branch and verification plan

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Remediation branch:** `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Documentation branch before this entry:** `docs/hybrid-llm-engine-direction@54b7d4b44c50bbd6e73dbc5d501a66e944e7203d`  
**Scope:** targeted remediation for existing findings BUILD-008 and BUILD-009; no new finding IDs.

### Governance and reconciliation

- Read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, GitHub CI module record, GitHub CI DEEP-AUDIT and FILE-REFERENCE before source continuation.
- Verified source `main` remains at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; docs branch is maintained separately.
- Retrieved complete recursive tree for the remediation branch: 856 entries, `truncated=false`.
- Existing 10-file `.github/` audit was not repeated. Only the two workflow files changed in this remediation branch.

### Changes implemented on the remediation branch

- `.github/workflows/ci.yml`: removed `deploy-preview-precheck` and `deploy-preview`. This removes Vercel secret injection, privileged PR deployment permissions, checkout/npm/report-generation execution in the Vercel job, and PR comment writes. Hosted PR report preview is intentionally suspended rather than replaced by an unverified privileged `workflow_run` consumer.
- `.github/workflows/publish-extension.yml`: manual dispatch defaults to dry-run and rejects `dry_run=false`; publisher jobs are release-event-only. A read-only build/verify job checks release ancestry and preserves npm release identity/version validation, builds VSIX/npm artifacts, and uploads a run-scoped artifact. VSIX and npm publishers consume that artifact without checkout or repository package scripts. Marketplace/Open VSX tokens are step-scoped. `id-token:write` is confined to the npm publisher job, which pins its setup-node/download-artifact actions, installs npm with lifecycle scripts disabled, checks SHA-512 integrity and idempotent registry state, and publishes with provenance.
- No application source, package manifest, dependency, package script or runtime configuration was changed.
- BUILD-013 remains a separate open finding; its release identity preflight was retained, but no finding was closed.

### Branch evidence

- `.github/workflows/ci.yml`: blob `8dc8cdf30e2cb1857708a8bd601807c08df34368`, 16,167 bytes, 450 lines.
- `.github/workflows/publish-extension.yml`: blob `0b408f04d6b7c47d1405b9259d257fd21bdcb15d`, 6,627 bytes, 237 lines.
- Both files were fetched from the remediation branch after the final update. The complete branch tree shows no other path changed from the base for this remediation.

### Verification planning and remaining gates

1. Static pre-merge: run a YAML/workflow parser and actionlint; verify expressions, job graph, artifact paths, outputs, shell quoting, npm CLI compatibility and permission scopes.
2. BUILD-008 PR negative test: confirm no Vercel secret or Vercel deploy job appears in a same-repository PR run; no write-capable token is present; no Vercel request or preview comment occurs.
3. BUILD-009 manual negative tests: dispatch from non-main with default dry_run=true and confirm build-only; dispatch with dry_run=false and confirm rejection; confirm publisher jobs do not start and build job has no OIDC permission.
4. BUILD-009 release positive rehearsal: use a controlled release/staging path; confirm ancestry preflight, exact run-scoped artifact, VSIX verification, npm integrity/idempotency, registry publication and provenance. Production publication requires explicit approval.
5. Settings: configure and inspect `npm-publish` Environment required reviewers and allowed refs; verify npm trusted publisher repository/workflow/environment tuple; verify Marketplace/Open VSX token scopes, Actions defaults and organization rulesets. A YAML Environment reference does not create protection rules.
6. Credential hygiene: inspect historical run/access logs for the former Vercel exposure window and rotate credentials if access history/scopes cannot exclude exposure; remove now-unused Vercel secrets only after preview suspension is accepted.
7. Post-merge: record merged workflow SHAs, run IDs/attempts, expanded permissions, artifact IDs/digests, publication outcomes and provenance URL. BUILD-008/009 closure requires the corresponding negative/positive tests and settings evidence.

### Execution boundaries

- Static workflow inspection and audit-document publication only.
- No workflow dispatch, CI run, YAML parser, actionlint, test, build, lint, typecheck, OIDC request, deployment, release or package publication executed.
- No application source or runtime files changed.
- BUILD-008 and BUILD-009 remain OPEN: remediation exists on an unmerged branch and verification is pending.


### GOV-GHCI-003 — post-publication verification

The documentation branch was fetched after the remediation records were published. The following committed files were re-fetched and their blob SHAs verified:

| Path | Verified blob SHA |
|---|---|
| `docs/current-architecture/github-ci/DEEP-AUDIT.md` | `4b38406653c7317693cb45d01dfd86b214e20815` |
| `docs/current-architecture/github-ci/FILE-REFERENCE.md` | `1bd58cca0af919d4377304080d26a12abd82c701` |
| `docs/current-architecture/github-ci/README.md` | `a0b7229e5e31f8da0c658d6c5d8987931439cb95` |
| `docs/current-architecture/governance/modules/github-ci.md` | `c58d4bc21ee9aba6ff1b11cf4aacf7cbeb862235` |
| `docs/current-architecture/governance/AUDIT-REGISTER.md` | `33f249d2b63260ef0bb35ca264e883d991f5a2cf` |
| `docs/current-architecture/governance/EVIDENCE-LOG.md` (before this verification append) | `043104bcf3c8019dc9ca39a6d7b5bfc54fe6735d` |

The source remediation branch was independently re-fetched: `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`. Complete recursive-tree comparison against `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` returned 856 entries on both trees, `truncated=false`, and exactly two changed blobs: `.github/workflows/ci.yml` and `.github/workflows/publish-extension.yml`. Their branch blob SHAs are `8dc8cdf30e2cb1857708a8bd601807c08df34368` and `0b408f04d6b7c47d1405b9259d257fd21bdcb15d`.

Targeted static assertions by inspection confirmed: no Vercel secret names or preview jobs in CI; manual dispatch defaults to true and rejects false; both publisher jobs require `release.published`; only one `id-token: write` declaration exists and it belongs to `publish-npm`; build checkout has `persist-credentials: false`; publisher jobs contain no checkout; npm artifact integrity and release identity checks are present. These are static observations, not executed workflow behavior.

This evidence-log entry itself must be fetched once after the commit to verify its final blob SHA and documentation branch HEAD. No workflow run or runtime verification was performed.


## 2026-11-25 — GOV-INFRA-001 / GOV-DEPMAP-001 / GOV-ADR-001

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch:** `security/remediate-build-008-009`  
**Source commit:** `86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Source base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Documentation branch HEAD before this append:** `e8fa7b82ec18080c7c0eaa9a809a448b9be7ecde`  
**Scope:** integrated infrastructure static audit, evidence-backed system dependency map, architecture-direction/ADR implementation reconciliation.

### Governance-first and state reconciliation

- Read governance README, AUDIT-REGISTER, AUDIT-METHOD and EVIDENCE-LOG before source continuation.
- Read infrastructure README/CI-CD and System Dependency Map; retrieved all infrastructure document paths, the Hybrid LLM Engine direction note and ADR-0001/0002/0003.
- Retrieved current source branch ref and complete recursive tree: 856 entries, `truncated=false`.
- Retrieved current `main` ref and compared complete tree path/blob pairs: exactly two source blobs differ from base, `.github/workflows/ci.yml` and `.github/workflows/publish-extension.yml`. No application source, package manifest, lockfile or build/test config changed in BUILD-008/009 remediation branch.
- Reused existing module audits rather than repeating unchanged 28 core, 135 server, 459 webview, 8 adapter, 47 E2E, 14 scripts and 10 `.github/` file reviews.

### Infrastructure integration

- Updated `infrastructure/BUILD-SYSTEM.md`: npm workspace/build graph, AsyncAPI generation, TypeScript/lint, esbuild outputs, Vite asset pipeline, prepack/vscode prepublish, package allowlist and external runtime dependencies.
- Updated `infrastructure/DEPENDENCIES.md`: root/server/webview direct dependency classes, optional native PTY, Fastify external CLI dependency, build-time pngjs and remaining transitive/bundle/SBOM questions.
- Updated `infrastructure/TESTING.md`: Vitest server/webview, Node package contract, Playwright E2E, suite counts/configuration, CI matrix and explicit non-execution status.
- Updated `infrastructure/README.md` and `infrastructure/CI-CD.md` to consolidate ownership and relate CI/release workflows to build/package/test steps.
- Static observations: root lockfile v3 has 816 package records; root workspaces are server/webview-ui; root Node engine is >=20 and .nvmrc selects 22; CI has a separate Node 20 package smoke job and 3 OS × 3 E2E shards. No package installation, build, dependency audit or artifact inspection was performed.
- Consolidated infrastructure status is PARTIAL, not fully audited: transitive dependency use, generated bundle/tarball/VSIX outputs, optional native install matrix and actual test outcomes remain OPEN.

### System dependency map

- Replaced the NOT AUDITED placeholder with a source-linked map for compile/build/package edges; VS Code and standalone host composition; Fastify/WebSocket/control-plane and terminal data-plane; AsyncAPI generation; client transport selection; provider/hook/consent flow; StateAdapter/FileStateAdapter persistence; CI/release integration and trust boundaries.
- Each direct source edge records source path, line range and blob SHA. Existing lifecycle/persistence/terminal details are reused as DOC-RECORDED where they were not re-opened.
- Status is STATIC MAP IN PROGRESS. Exhaustive transitive imports, full terminal/process ownership, multi-process/runtime lifecycle and external settings remain OPEN.

### Architecture and ADR reconciliation

- Created `ARCHITECTURE-ADR-RECONCILIATION.md` and module status record.
- The current source tree contains no C/C++ source or CMake build files and no Android/Gradle/AndroidManifest project. Current provider registry is Claude-only; no generic Main Agent/worker scheduler or outbound model inference SDK was identified in the reviewed server contract/dependency evidence. Existing browser mobile E2E is not a native Android app.
- ADR-0001: static implementation alignment found for immediate consent send, tri-state consent/revision semantics, process-wide queue, privileged ask gate and client tour state. Tests were inventoried but not run.
- ADR-0002: static alignment found between CONTEXT.md Directory vocabulary and AsyncAPI/generated wire contract; legacy persisted `folderName` remains a read migration fallback.
- ADR-0003: `repeat` wire/persisted/rendered path and legacy hue migration are source-verified; full `characterSpritesLoaded` path remains PARTIAL.
- All 12 Hybrid LLM Engine design questions remain OPEN. The directional note is not a frozen implementation spec. No new finding IDs were created.

### Key source evidence identities

- `package.json`: `66efe28fd2927466c5f8283c6e26fe63da427079`
- `package-lock.json`: `e183f6eced2e598496010a79c56a19f32e3dbe83`
- `esbuild.js`: `450d0d3a1e2e194d47d5bb598c1669b9f3c33955`
- `webview-ui/vite.config.ts`: `b47531773bf6fea57fe6d46a6291133371aa56ea`
- `core/asyncapi.yaml`: `ff1247118819fe68852da11996c660df1726aa66`
- `server/src/server.ts`: `e3616e30f635ce5074a62829cd518af455b24009`
- `server/src/httpServer.ts`: `ad5104a14233aa29834e008833a0f21458fc887b`
- `server/src/clientMessageHandler.ts`: `d7ae8235ee75de63b1716a4b67f27d69023f5d96`
- `adapters/vscode/PixelAgentsViewProvider.ts`: `94d1ab2e1427377e67ff430a852d49863e5e4d87`
- `webview-ui/src/transport/index.ts`: `066cb0b06dd1c9a00c74dda27a21fe0c49e1b14a`
- `server/src/providers/hook/consentExecutor.ts`: `523dbde800d275c8f070a7d44ac48956e4034b63`
- `server/src/agentPersistence.ts`: `0dcced81be9762d924ebed02aeea3c6c24ece0bf`

### Documentation publication and execution boundary

Documentation changes are published on the governance branch only; application source and the remediation branch were not modified. Files updated/created in this session:
- `infrastructure/BUILD-SYSTEM.md`
- `infrastructure/DEPENDENCIES.md`
- `infrastructure/TESTING.md`
- `infrastructure/README.md`
- `infrastructure/CI-CD.md`
- `SYSTEM-DEPENDENCY-MAP.md`
- `ARCHITECTURE-ADR-RECONCILIATION.md`
- governance module records for infrastructure, system dependency map and architecture/ADR
- governance README, AUDIT-REGISTER and current-architecture README

No test, build, typecheck, lint, npm install, generation, package, bundle analysis, dependency audit, benchmark, CI dispatch, network experiment, OIDC request, PTY experiment or runtime reproduction was executed. No application source, package manifest, dependency or workflow changed during these three audit workstreams.


### GOV-INFRA-001 / GOV-DEPMAP-001 / GOV-ADR-001 — post-publication verification

Documentation branch HEAD after publication: `23e6987fc97226bea3819af12952fc2af0602966`. The following records were re-fetched from that branch and their blob identities verified:

| Path | Verified blob SHA |
|---|---|
| `infrastructure/BUILD-SYSTEM.md` | `fe9e281102a6f3ba4ba7a51e0d4811d83e46fe46` |
| `infrastructure/DEPENDENCIES.md` | `e58935bdd61804ba543c8b2ba7da36d25e132570` |
| `infrastructure/TESTING.md` | `14f7035502ac277c449662f5542833824057ac61` |
| `infrastructure/README.md` | `d4595a16f03aa61064348dba4a8c853a9f09ce32` |
| `infrastructure/CI-CD.md` | `08d3b9eab01b573df0c939e8858a23a1a2b921ab` |
| `SYSTEM-DEPENDENCY-MAP.md` | `167af5aedeeeaa39c4c7ede59054a7fed15c6ab0` |
| `ARCHITECTURE-ADR-RECONCILIATION.md` | `2ffd3c3fbe90681ef04bf7f9d7440b4f06050fcf` |
| `governance/modules/infrastructure.md` | `5f3442105570434237ccf44a87fd14cbad5a632a` |
| `governance/modules/system-dependency-map.md` | `9e3f45b2e4663dc13c0c08c539fb0198d7551872` |
| `governance/modules/architecture-adr.md` | `911f1bc32904e6dcaaea26b8eb3de48cd7fc06d1` |
| `governance/README.md` | `44b36635fc2ea22d1bebbde0c8628a5e95e6b101` |
| `governance/AUDIT-REGISTER.md` | `a71940e27443bdd738f96ee507a97a119e52dd90` |
| `current-architecture/README.md` | `a688ace86c2611ab5ff42f5244b371a9c1229755` |
| `governance/EVIDENCE-LOG.md` before this verification append | `d8094ed85da4358658d43e98db8a17bc0b47f18f` |

All 14 updated/created files returned non-empty content and the expected source-snapshot/status references where applicable. The evidence-log blob above is the pre-verification version; this append must be fetched once after commit to establish its final blob SHA and final documentation branch HEAD.

No runtime or toolchain verification was performed; this publication check verifies documentation commits and content identities only.


### GOV-INFRA-001 — evidence correction and final re-fetch

During final SHA spot-check, the `server/vitest.config.ts` blob SHA transcribed in the Build System table was corrected to the directly fetched value `b8d6265259e3abcec1d93945bbf42c43618b62d0`. The corrected `infrastructure/BUILD-SYSTEM.md` was re-fetched after publication; its final blob SHA is `dc971936695c9fd59503a46f68b8cad98f19453c`. This replaces the earlier Build System blob SHA `fe9e281102a6f3ba4ba7a51e0d4811d83e46fe46` in the post-publication table above. No source finding or implementation conclusion changed.


### GOV-INFRA-001 — current-architecture index baseline clarification

The current-architecture index previously displayed only the historical module baseline. It now labels that baseline as historical and records the latest cross-layer static source snapshot `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`, based on `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. The updated index was re-fetched and verified as blob `511a7e330079e7ffc5da9f9cfec2a8071f17c917`.


## 2026-11-26 — GOV-CROSS-LAYER-001: final branch-state and governance-pointer reconciliation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Documentation branch:** `docs/hybrid-llm-engine-direction`  
**Source branch:** `security/remediate-build-008-009`  
**Source commit:** `86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Source base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Scope:** read-only branch/tree reconciliation and correction of one stale governance continuation pointer.

### Governance and history

- Retrieved the applicable governance README, audit register, audit method and evidence log from `docs/current-architecture/governance/`, plus the infrastructure, system-dependency-map and architecture/ADR module records and their technical documents.
- The repository does not contain the alternate root-level paths `governance/README.md`, `governance/AUDIT-REGISTER.md`, `governance/AUDIT-METHOD.md` or `governance/EVIDENCE-LOG.md`; the active governance source of truth is under `docs/current-architecture/governance/`.
- Historical GOV-INFRA-001, GOV-DEPMAP-001 and GOV-ADR-001 records are present in the evidence log. No historical finding IDs were reused or closed.

### Current repository state

- Verified refs: `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`; documentation branch before this correction `b552ac64198a1494fc4450750ebd530d2771bf49`.
- Retrieved complete recursive trees for main and the remediation branch: both contain 856 entries, `truncated=false`.
- Compared all blob path/SHA pairs: 739/739 source blobs are present on both; exactly two blobs differ, `.github/workflows/ci.yml` and `.github/workflows/publish-extension.yml`. No source additions or removals.
- Commit comparison reports remediation branch ahead of main by 6 commits, behind by 0; the two workflow files are the only changed files in the source tree.
- No open pull request was returned for head branch `security/remediate-build-008-009` by the repository-scoped PR search.

### Governance correction

- The consolidated register already reported the system dependency map as STATIC MAP IN PROGRESS, but continuation item 6 still said the map itself was NOT AUDITED. That stale sentence contradicted the published map and module record.
- Updated item 6 to reflect the actual evidence: major compile/build, host composition, protocol, persistence, provider, transport and trust-boundary edges are SOURCE-VERIFIED; exhaustive transitive imports/call graph, complete terminal/process ownership and runtime lifecycle remain OPEN.
- Updated register blob: `fba035474812a7ed2b946aa16e1472a46aed99f9` (commit `26eae50d351a2245a76c9f50cd31df848f66efc0`).

### Verification boundary

- This was a static Git/governance reconciliation only. No source files, workflows, manifests, dependencies or settings were changed.
- No tests, build, typecheck, lint, package, dependency/SBOM generation, CI dispatch, release rehearsal, OIDC operation or runtime experiment was executed.
- BUILD-008/009 remain OPEN pending negative/positive workflow verification and merge. Infrastructure remains PARTIAL; system dependency map remains STATIC MAP IN PROGRESS; Architecture/ADR remains PARTIAL with 12 future-engine decisions OPEN.
- Branch merge/closure was not performed.


## 2026-11-26 — GOV-TECH-VERIFY-001: authorized verification attempt and execution blockers

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Target source:** `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Scope:** authorized technical-verification attempt, static workflow control-flow review, and evidence recording. No application/workflow changes.

### Governance and source reconciliation

- Reused governance and historical records reviewed in the preceding continuation: active governance lives under `docs/current-architecture/governance/`; historical GOV-GHCI-001/002 and GOV-INFRA-001/GOV-DEPMAP-001/GOV-ADR-001 remain the evidence baseline.
- Re-fetched remediation source files and verified:
  - `package.json` blob `66efe28fd2927466c5f8283c6e26fe63da427079`
  - `.nvmrc` blob `2bd5a0a98a36cc08ada88b804d3be047e6aa5b8a` (Node 22)
  - `.github/workflows/ci.yml` blob `8dc8cdf30e2cb1857708a8bd601807c08df34368`
  - `.github/workflows/publish-extension.yml` blob `0b408f04d6b7c47d1405b9259d257fd21bdcb15d`
- Root package scripts identify typecheck, lint, format, webview/server/package-contract tests, extension/webview builds, npm package verification and E2E execution targets. These are inventory only, not execution results.

### Execution attempt and observed blockers

- Queried GitHub Actions runs for branch `security/remediate-build-008-009`: API returned `total_count=0`, no workflow runs.
- Queried commit check-runs for `86d3b1d...`: `total_count=0`; commit status endpoint returned `total_count=0` and `state=pending`.
- Available GitHub connector operations expose workflow-run/job/artifact reads and log retrieval, but no workflow dispatch or remote command execution action.
- Attempted an isolated repository clone to `/tmp/k41-nexus-prototype`; clone could not resolve `github.com` (`Could not resolve host: github.com`). No working tree was obtained.
- Therefore no safe execution environment was available to install dependencies or run project commands. This is an environment/tool-access blocker, not a test failure.

### Static finding discovered during verification review

- Added **BUILD-014** to the CI/release deep audit: `validate_spec`, `messages_drift` and `e2e_inventory_drift` in the remediation branch CI workflow use `continue-on-error: true` (workflow lines 56-82), but the final gate exports/checks only checkout, setup, install, typecheck, lint, webview tests, format, build, npm package and server tests (lines 209-233).
- The source comments label the protocol checks as blocking, but their outcomes are not consumed by the final gate. This is a HIGH-confidence STATIC-FINDING; no failed workflow step was executed or reproduced.
- Affected workflow blob: `8dc8cdf30e2cb1857708a8bd601807c08df34368`.
- Recommended correction is to make these steps naturally fail the job or explicitly include their outcomes in the final gate and summary. No source fix was applied.

### Results and verification labels

- **TREE-VERIFIED:** remediation tree was previously verified complete (856 entries, `truncated=false`); only the two workflow blobs differ from main.
- **SOURCE-VERIFIED:** package manifest, Node version selector and both remediation workflows re-fetched at the recorded blob identities.
- **STATIC-FINDING:** BUILD-014 recorded; BUILD-008/009 implementation remains static-only.
- **TEST-INVENTORIED:** project command inventory re-confirmed from `package.json`.
- **TEST-EXECUTED:** none.
- **BUILD / TYPECHECK / LINT / PACKAGE / E2E EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.
- **Workflow negative tests / release positive rehearsal / OIDC / registry publication:** not run.

### Documentation publication

- Updated `github-ci/DEEP-AUDIT.md` with BUILD-014.
- Updated `governance/modules/github-ci.md` and consolidated `governance/AUDIT-REGISTER.md` to include BUILD-014 as OPEN.
- No application source, package manifest, dependency, workflow, GitHub setting or release state was changed.
- BUILD-008/009 remain OPEN pending actual PR/manual negative workflow tests, controlled release positive rehearsal, and GitHub/npm settings evidence. BUILD-014 remains OPEN pending source correction and workflow verification.
- This entry must be re-fetched after commit to verify its final blob SHA. No merge or branch closure was performed.


## 2026-11-26 — GOV-DOC-CONSOLIDATION-002: cross-record reconciliation and branch consolidation preparation

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Documentation branch:** `docs/hybrid-llm-engine-direction` (pre-merge publication branch)  
**Target canonical branch:** `main`  
**Source baseline:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Additional static CI baseline:** `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`

### Governance and state reviewed

- Re-fetched governance README, Audit Register, Audit Method, Evidence Log and the infrastructure, dependency-map, architecture/ADR and GitHub CI module records.
- Re-fetched infrastructure README, TESTING, CI/CD and Architecture/ADR reconciliation.
- Verified branch refs before edits: `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`, `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`, `docs/hybrid-llm-engine-direction@6cea20c4aeccde46da558356455ebc80c7c0c79c`.
- Historical module ledgers continue to report: core 28/28; server 135/135; webview 459/459 with 1 font NOT VERIFIED and 143 assets only header/signature verified; adapters 8/8 static; E2E 47/47 inventory with 21 specs / 152 assertions TEST-INVENTORIED; scripts 14/14; historical `.github/` 10/10. These are scoped coverage claims, not runtime validation.

### Consolidation corrections

- Synchronized BUILD-014 into infrastructure governance ownership and references. BUILD-001–BUILD-007 remain owned by scripts; BUILD-008–BUILD-014 remain owned by GitHub CI/release.
- Corrected infrastructure TESTING and CI/CD descriptions: AsyncAPI validation, generated-message drift and E2E inventory drift use `continue-on-error: true` and are absent from the final failure gate. Their failure is not blocking in the audited workflow snapshot; BUILD-014 remains STATIC-FINDING / OPEN.
- Removed the duplicate Architecture/ADR status row from the Audit Register; the single repository docs/ADRs row remains the consolidated entry and points to its owning module/reconciliation record.
- Preserved Architecture/ADR status as PARTIAL, with three ADRs reconciled and 12 future-engine decisions OPEN.
- Preserved infrastructure PARTIAL and system dependency map STATIC MAP IN PROGRESS; no status was promoted based on documentation cleanup.
- No finding IDs were created, closed or reused in this consolidation pass.

### Files published in this session

- `governance/README.md`
- `governance/AUDIT-REGISTER.md`
- `governance/modules/infrastructure.md`
- `governance/modules/github-ci.md`
- `infrastructure/README.md`
- `infrastructure/TESTING.md`
- `infrastructure/CI-CD.md`

### Verification boundary and branch plan

- Every listed published file is to be re-fetched and checked for the expected BUILD-014 wording and updated SHA; publication verification is recorded only after that fetch.
- No source code, package manifest, dependency, workflow, GitHub setting or release state was modified.
- No tests, build, typecheck, lint, package, dependency/SBOM generation, workflow dispatch, release rehearsal, OIDC operation or runtime experiment was executed.
- No branch was merged or deleted during this documentation pass. The remaining target is one canonical `main` branch; PR/merge operations and branch deletion capability must be verified separately. The available connector exposes PR merge and ref update but no branch-delete operation.


## 2026-11-26 — GOV-DOC-CONSOLIDATION-002 publication verification

- Documentation PR [#1](https://github.com/K41-Origin/k41-nexus-prototype/pull/1) was squash-merged into `main`.
- Merge commit: `beb404fe2aa14506926f6065186387ef734beaac`; verified `refs/heads/main` points to this commit.
- The PR publishes 65 documentation files (8,294 additions, no deletions). No application source or workflow files were changed by this PR.
- The updated governance README, Audit Register, Evidence Log, infrastructure module record, GitHub CI module record, infrastructure README, TESTING and CI/CD documents were fetched again from `main`; the BUILD-014 correction and consolidation session are present in the published versions.
- PR #1 is CLOSED / MERGED. The canonical governance target is now `main`.
- The security remediation branch remains separate because its CI workflow snapshot contains BUILD-014: three `continue-on-error` protocol/inventory checks are omitted from the final failure gate. Merging that branch before resolving this known CI gating defect would publish a known false-green path. No source fix was applied because this pass was authorized for verification/documentation consolidation, not an application/workflow patch.
- The connector exposes branch creation/ref update but no branch-delete operation. Therefore branch deletion cannot be claimed or performed through the available interface. The remaining security branch requires BUILD-014 correction/verification and then merge; only after that can it be removed through a branch-delete-capable GitHub interface.
- No tests, build, typecheck, lint, package, workflow dispatch, release rehearsal, OIDC operation or runtime experiment was executed in this documentation publication step.


## 2026-11-26 — GOV-DOC-CONSOLIDATION-003: final BUILD cross-reference reconciliation

- Re-fetched module records and owning deep-audit/file-reference documents for core, server, webview-ui, adapters, E2E, scripts and GitHub CI/release.
- Found stale cross-scope BUILD ranges in the E2E deep audit and E2E module record (they stopped at BUILD-013) and stale BUILD-008–BUILD-013 wording in the GitHub CI deep-audit header. Updated all to include BUILD-014.
- Updated the scripts module cross-scope reference so its BUILD range includes the complete CI/release namespace while preserving ownership of BUILD-001–BUILD-007 in scripts.
- Reconciled the Audit Register next action: BUILD-014 must be resolved before merging the BUILD-008/009 remediation PR; verification gates remain OPEN.
- Re-fetched and verified the updated GitHub CI deep audit, E2E deep audit, E2E module record and scripts module record on `main` after publication.
- Remaining coverage boundaries are unchanged: webview font TTF internals remain NOT VERIFIED; 143 public binary assets remain header/signature/metadata verified only; runtime/build/test verification remains unexecuted.
- No source, workflow, package manifest, dependency, GitHub setting or release state was changed in this documentation-only reconciliation.


## 2026-11-26 — GOV-BRANCH-CONSOLIDATION-001: canonical main selected

- Canonical branch selected by the repository owner: `main`.
- Current verified refs before this evidence append:
  - `main@2718891011458fcbc7b8de11d4db7f134113fc32`
  - `docs/hybrid-llm-engine-direction@ff15c7a28a515cc85af8e5e2c1f79a0ff362ddb4`
  - `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`
- Documentation PR #1 was squash-merged; all 65 audit documentation files are now present on `main`.
- Security PR #2 was opened to consolidate the two workflow changes. It is intentionally NOT merged because BUILD-014 is an identified, uncorrected CI failure-gate defect. BUILD-008/009 also remain OPEN pending their actual workflow verification gates.
- The documentation branch and security branch refs still exist. The available GitHub connector has no branch/ref deletion action; it cannot delete either ref. No destructive workaround or force-update was attempted.
- The security workflow source was not modified in this session. A source patch to resolve BUILD-014 requires explicit authorization; after correction, static review and workflow verification must precede PR #2 merge.
- Therefore the requested end state (only `main`) is NOT YET ACHIEVED. Current branch refs remain three: `main`, `docs/hybrid-llm-engine-direction`, and `security/remediate-build-008-009`.

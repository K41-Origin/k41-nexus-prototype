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

# Webview UI Module Audit Record

**Status:** IN PROGRESS — complete tracked-file inventory/SHA reconciliation; 34/34 ClientMessage and 46/46 ServerMessage variants statically traced; field-level validation/authorization/persistence/side-effect static audit completed in GOV-WEBVIEW-011; static lifecycle/reconnect/state-reconciliation trace completed in GOV-WEBVIEW-012; runtime/cross-client verification remains open.  
**Audit sessions:** `GOV-WEBVIEW-001` through `GOV-WEBVIEW-012` (2026-11-25)  
**Source baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Historical verification commit:** `8fc045de9ddae905cc131498d918ddbd64cc8bca`  
**Latest source verification:** `main` — `b1104a808cf70382c2bd14a94d372d907a500cda` (tree `97940dabd84264b6281917b11928ebae6156752c`, `truncated: false`, GOV-GAP-CLOSURE-001)  
**Inventory:** 459/459 tracked blobs reconciled; no path or blob-SHA differences from the baseline.  
**Evidence:** [GOV-WEBVIEW-001](../EVIDENCE-LOG.md#2026-11-25--gov-webview-001-webview-ui-inventory-and-governance-reconciliation).

## Document ownership

This is the governance summary, not a duplicate technical audit.

| Concern | Owning document |
|---|---|
| Consolidated status | [Audit register](../AUDIT-REGISTER.md) |
| Session evidence and repository snapshot | [Evidence log](../EVIDENCE-LOG.md) |
| Detailed technical findings and continuation notes | [DEEP-AUDIT.md](../../webview-ui/DEEP-AUDIT.md) |
| Per-file path, blob SHA, byte size and review status | [FILE-REFERENCE.md](../../webview-ui/FILE-REFERENCE.md) |
| Control-plane message routes and validation map | [PROTOCOL-MATRIX.md](../../webview-ui/PROTOCOL-MATRIX.md) |
| Public asset metadata and validation boundaries | [ASSET-INVENTORY.md](../../webview-ui/ASSET-INVENTORY.md) |
| Module composition and feature boundaries | [MODULE-OVERVIEW.md](../../webview-ui/MODULE-OVERVIEW.md) |
| UI state ownership and consistency observations | [STATE-MANAGEMENT.md](../../webview-ui/STATE-MANAGEMENT.md) |
| Canvas/rendering and cache observations | [RENDERING-ENGINE.md](../../webview-ui/RENDERING-ENGINE.md) |
| Package and internal dependency map | [DEPENDENCIES.md](../../webview-ui/DEPENDENCIES.md) |
| Transport behavior and lifecycle | [TRANSPORT.md](../../webview-ui/TRANSPORT.md) |

Detailed findings and file-level status remain in their owning documents. Do not copy the 459-row inventory or full finding analysis into this record or the register.

## Inventory and SHA reconciliation

At historical verification commit `8fc045de9ddae905cc131498d918ddbd64cc8bca` (the later source verification at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` is recorded in GOV-WEBVIEW-002/008):

- Recursive repository tree: `truncated: false`.
- `webview-ui/` contains **459 tracked blobs**:
  - 302 under `webview-ui/src/`;
  - 92 under `webview-ui/public/`;
  - 54 under `webview-ui/test/`;
  - 11 root/package/build/config files.
- The baseline tree at `4dd14d612fd37d89ffbbc977959f572395c4eec8` contains the same 459 paths.
- Current tree vs baseline: **0 added, 0 removed, 0 changed blob SHAs**.
- Current tree vs `FILE-REFERENCE.md`: **459/459 paths present, 0 missing, 0 extra, 0 SHA mismatches**.
- No tracked webview file needs a repeat review because of a source change. Historical review evidence remains applicable to unchanged content.

The exact per-file identities and statuses remain in [FILE-REFERENCE.md](../../webview-ui/FILE-REFERENCE.md). This reconciliation verifies inventory completeness and source identity; it does not upgrade review status.

## Historical coverage status

The 459 ledger rows currently classify as:

| Ledger status | Files | Meaning |
|---|---:|---|
| `REVIEWED` | 261 | Prior 254 plus 7 remaining webview source files fully re-fetched and statically reviewed in GOV-WEBVIEW-008 |
| `FETCHED / REVIEWED REPORTED` | 0 | All seven remaining rows directly reviewed in GOV-WEBVIEW-008; no rows remain in this status |
| `REVIEWED REPORTED` | 0 | All 9 character UI rows directly reviewed in GOV-WEBVIEW-007 |
| `TEST-INVENTORIED` | 54 | All 54 test files have assertion-level inventory/review evidence (7 prior + 47 in GOV-WEBVIEW-009); not executed |
| `FETCHED / INVENTORIED` | 0 | No remaining test files in this status after GOV-WEBVIEW-009 |
| `PNG CHUNK CRC VERIFIED` | 141 paths / 132 unique blobs | Signature, chunk bounds, IHDR/IDAT/IEND and every chunk CRC verified; IDAT pixel decompression remains OPEN |
| `ICO STRUCTURE VERIFIED` | 1 | Directory bounds and 3 embedded PNG chunk CRCs verified |
| `JPEG MARKERS VERIFIED` | 1 | SOI/SOF0/SOS/EOI and dimensions verified; pixel decode remains OPEN |
| `CLOSED BY SCOPE ACCEPTANCE` | 1 | `public/fonts/FSPixelSansUnicode-Regular.ttf` accepted as a font asset for inventory purposes; internal structure and rendering were not validated |

These categories sum to 459. GOV-WEBVIEW-005 directly reviewed the 32 component rows previously left as reported-review; together with the 22 component rows already REVIEWED, `src/components/` is 54/54 REVIEWED. GOV-WEBVIEW-006 directly reviewed the 14 remaining reported-review hook rows; together with the 10 previously REVIEWED hook rows, `src/hooks/` is 24/24 REVIEWED. All seven previously fetched/reported source rows elsewhere in webview source were directly reviewed in GOV-WEBVIEW-008; they were the same seven rows described in the earlier continuation note, not a second independent group. The 9 character UI rows are also directly reviewed. All 54 test files now have assertion-level inventory/review evidence; the 47 formerly inventoried-only files were completed in GOV-WEBVIEW-009. No tests were executed. Do not repeat completed work wholesale. The font audit item is closed by explicit scope acceptance (2026-11-26), not by technical validation; do not represent its internals or rendering as verified. Reopen only if font integrity, provenance, Unicode coverage or runtime rendering becomes an explicit requirement. Full pixel decompression/browser loading for PNG/JPEG assets also remains open.

## Documentation verification

All ten existing webview architecture documents were fetched and confirmed non-empty at the historical verification commit: README, FILE-REFERENCE, DEEP-AUDIT, ASSET-INVENTORY, PROTOCOL-MATRIX, MODULE-OVERVIEW, STATE-MANAGEMENT, RENDERING-ENGINE, DEPENDENCIES and TRANSPORT.

The existing documentation remains the technical source for module details. This governance record consolidates status and ownership; it does not replace those documents.

## Findings and continuation

GOV-WEBVIEW-005 adds three static component observations for future technical prioritization: `WV-C12` (touch cancellation shares the touch-end completion path), `WV-C13` (modal dialog semantics and focus lifecycle need verification), and `WV-C14` (loading-art rejection has no local recovery path). GOV-WEBVIEW-006 adds `WV-C15` (editor keyboard shortcuts also receive events from text inputs) and `WV-C16` (Shift+Z redo key comparison is lowercase-only). GOV-WEBVIEW-007 completes the 9 character UI line-coverage gaps; no new finding ID was warranted. These are OPEN static findings, not runtime-reproduced defects or fixes. It also adds component-level lifecycle evidence to the existing `WV-C03/WV-C06` concern. These are OPEN static findings, not runtime-reproduced defects or fixes.

Preserve existing `WV-` findings and their states in [DEEP-AUDIT.md](../../webview-ui/DEEP-AUDIT.md). The reconnect trace in GOV-WEBVIEW-001 adds `WV-RESYNC-01`, a static finding about applying a server seat snapshot while the client still has its pre-reconnect layout. GOV-WEBVIEW-010 adds `WV-PROTO-01` (area mappings are not fanned out to other connected clients) and `WV-PROTO-02` (external asset reload is requester-only). All remain open static findings; none is runtime reproduced.

The historical SHA reconciliation for all 62 reported-review rows is complete. GOV-WEBVIEW-005 directly reviewed the 32 component rows with missing per-file coverage evidence; all 54 files under `webview-ui/src/components/` are now REVIEWED. GOV-WEBVIEW-006 completed all 24 hooks, and GOV-WEBVIEW-007 completed all 33 character source files. GOV-WEBVIEW-008 directly reviewed the final seven reported-review source rows elsewhere in `webview-ui/src/`. There are now **no reported-review source rows remaining**.

The following is the authoritative continuation pointer; older continuation notes in DEEP-AUDIT are historical and superseded where later GOV-WEBVIEW sessions completed their stated scope:

1. GOV-WEBVIEW-011 completed the per-field validation, authorization/read-only, persistence and side-effect matrix for all 34 ClientMessage variants. Reuse `WV-C08` / `F-08`; do not repeat this work.
2. GOV-WEBVIEW-012 completed the static cross-layer lifecycle/reconnect/state-reconciliation trace. Do not repeat the producer-consumer or lifecycle trace without changed source or a named evidence gap.
3. Preserve `WV-PROTO-01/02/03`, `WV-APP-01`, `WV-RESYNC-01/02`, `WV-TR-01`, `WV-VITE-01`, `WV-C03/WV-C06`, `WV-C12`–`WV-C17` and `WV-TEST-01` as open static/test-quality findings or verification questions in the deep audit.
4. Obtain the TTF binary and validate SFNT tables/glyph coverage. PNG chunk CRC integrity is now verified for 132 unique blobs/141 paths; full IDAT pixel decompression and browser loading remain OPEN.
5. Perform browser, cross-client and reconnect runtime verification only when explicitly authorized; record environment and exact observations.
6. Integrated infrastructure review and system dependency/lifecycle mapping are owned by their respective documents and the repository audit register.

## Explicit limitations

- Static source and documentation investigation only.
- No tests, build, lint, typecheck, protocol generation or runtime experiments executed.
- No application source, assets, package manifest or configuration changed.
- Binary header verification does not establish full image integrity. The TTF item is separately closed by scope acceptance only; this does not establish font-file integrity.
- The audit is not complete merely because all paths and SHAs reconcile.


## Latest continuation — GOV-WEBVIEW-010

- Static producer → consumer tracing is complete for all 34 ClientMessage and 46 ServerMessage variants. The detailed UI producer, host ingress, server producer, recipient/fan-out, validation and persistence map is owned by PROTOCOL-MATRIX.md.
- New static findings: WV-PROTO-01 (shared Directory-to-Area mappings are persisted without notifying already-connected peers) and WV-PROTO-02 (external asset directory reload is delivered only to the requesting client). Both are OPEN and conditional on multiple connected standalone clients; neither has been runtime reproduced.
- The five standalone-unhandled ClientMessage variants are explicitly reconciled with VS Code host actions/local browser behavior. focusAgent is sent by App in both runtimes; standalone intentionally no-ops the server-side action.
- Existing WV-C08 / server F-08 remain the canonical validation gap. Runtime schema enforcement, complete field-by-field authorization, and some shared-setting fan-out questions remain open.
- No source, test, build, package, asset or configuration files were modified; no tests/build/lint/typecheck/runtime experiments were executed.


## Latest continuation — GOV-WEBVIEW-012

- Static cross-layer lifecycle tracing is complete for standalone control-plane reconnect/snapshot replay, VS Code iframe readiness and restore ordering, and the separate per-agent terminal data plane.
- Fresh recursive source tree: 856 entries / 739 blobs / 117 trees; `truncated=false`. Webview ledger 459/459 and adapter ledger 8/8 path/blob SHA pairs match with zero missing/extra/mismatched entries. Server tree contains 135 blobs; selected cross-layer source SHAs match the tree and server governance records its prior 135/135 reconciliation.
- Existing `WV-RESYNC-01` is extended with a VS Code first-hydration ordering path; its canonical ID is preserved. New `WV-RESYNC-02` records replay of all queued ClientMessages after standalone reconnect without expiry/idempotency classification. Both are OPEN static findings.
- `WV-APP-01`, `WV-TR-01`, `WV-PROTO-01/02/03`, adapter `A-03/A-05/A-06`, and server `F-01/F-03/F-05` remain open; no duplicates or closures.
- No tests, builds, lint, typecheck, browser/VS Code runtime, reconnect fault injection or PTY experiments were executed. No application source was modified.

## Next work

1. Determine the intended policy for commands issued during control-plane disconnection and the authoritative order for VS Code agent restore/snapshot/layout.
2. Preserve and cross-link `WV-RESYNC-01/02`, `WV-APP-01`, `WV-TR-01`, `WV-PROTO-01/02/03` and adapter/server lifecycle findings.
3. Runtime reproduction requires explicit authorization. Do not repeat the completed producer-consumer or field-level protocol audits.

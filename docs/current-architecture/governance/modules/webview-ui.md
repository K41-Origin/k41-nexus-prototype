# Webview UI Module Audit Record

**Status:** IN PROGRESS — complete tracked-file inventory and SHA reconciliation; 34/34 ClientMessage and 46/46 ServerMessage variants statically traced; field-level and runtime/cross-client verification remain open.  
**Audit sessions:** `GOV-WEBVIEW-001` through `GOV-WEBVIEW-010` (2026-11-25)  
**Source baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Historical verification commit:** `8fc045de9ddae905cc131498d918ddbd64cc8bca`  
**Latest source verification:** `main` — `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` (tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated: false`, per GOV-WEBVIEW-002/008 evidence)  
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
| `BINARY HEADER VERIFIED` | 143 | Header/signature/metadata checks only, as specified per file |
| `NOT VERIFIED` | 1 | `public/fonts/FSPixelSansUnicode-Regular.ttf` internals remain unavailable |

These categories sum to 459. GOV-WEBVIEW-005 directly reviewed the 32 component rows previously left as reported-review; together with the 22 component rows already REVIEWED, `src/components/` is 54/54 REVIEWED. GOV-WEBVIEW-006 directly reviewed the 14 remaining reported-review hook rows; together with the 10 previously REVIEWED hook rows, `src/hooks/` is 24/24 REVIEWED. All seven previously fetched/reported source rows elsewhere in webview source were directly reviewed in GOV-WEBVIEW-008; they were the same seven rows described in the earlier continuation note, not a second independent group. The 9 character UI rows are also directly reviewed. All 54 test files now have assertion-level inventory/review evidence; the 47 formerly inventoried-only files were completed in GOV-WEBVIEW-009. No tests were executed. Do not repeat completed work wholesale. The single unverified font must remain open until its binary content is actually available.

## Documentation verification

All ten existing webview architecture documents were fetched and confirmed non-empty at the historical verification commit: README, FILE-REFERENCE, DEEP-AUDIT, ASSET-INVENTORY, PROTOCOL-MATRIX, MODULE-OVERVIEW, STATE-MANAGEMENT, RENDERING-ENGINE, DEPENDENCIES and TRANSPORT.

The existing documentation remains the technical source for module details. This governance record consolidates status and ownership; it does not replace those documents.

## Findings and continuation

GOV-WEBVIEW-005 adds three static component observations for future technical prioritization: `WV-C12` (touch cancellation shares the touch-end completion path), `WV-C13` (modal dialog semantics and focus lifecycle need verification), and `WV-C14` (loading-art rejection has no local recovery path). GOV-WEBVIEW-006 adds `WV-C15` (editor keyboard shortcuts also receive events from text inputs) and `WV-C16` (Shift+Z redo key comparison is lowercase-only). GOV-WEBVIEW-007 completes the 9 character UI line-coverage gaps; no new finding ID was warranted. These are OPEN static findings, not runtime-reproduced defects or fixes. It also adds component-level lifecycle evidence to the existing `WV-C03/WV-C06` concern. These are OPEN static findings, not runtime-reproduced defects or fixes.

Preserve existing `WV-` findings and their states in [DEEP-AUDIT.md](../../webview-ui/DEEP-AUDIT.md). The reconnect trace in GOV-WEBVIEW-001 adds `WV-RESYNC-01`, a static finding about applying a server seat snapshot while the client still has its pre-reconnect layout. GOV-WEBVIEW-010 adds `WV-PROTO-01` (area mappings are not fanned out to other connected clients) and `WV-PROTO-02` (external asset reload is requester-only). All remain open static findings; none is runtime reproduced.

The historical SHA reconciliation for all 62 reported-review rows is complete. GOV-WEBVIEW-005 directly reviewed the 32 component rows with missing per-file coverage evidence; all 54 files under `webview-ui/src/components/` are now REVIEWED. GOV-WEBVIEW-006 completed all 24 hooks, and GOV-WEBVIEW-007 completed all 33 character source files. GOV-WEBVIEW-008 directly reviewed the final seven reported-review source rows elsewhere in `webview-ui/src/`. There are now **no reported-review source rows remaining**.

The next work is (authoritative continuation pointer; older continuation notes in DEEP-AUDIT are historical and superseded by GOV-WEBVIEW-008):

1. Complete the remaining per-field validation, authorization/read-only, persistence and side-effect matrix. Reuse `WV-C08` and server `F-08`; do not create duplicate schema findings. The 34 ClientMessage and 46 ServerMessage producer/consumer routes are statically mapped in PROTOCOL-MATRIX.md.
2. Confirm the intended multi-client consistency contract for `WV-PROTO-01` and `WV-PROTO-02`; runtime reproduction requires explicit authorization.
3. Continue host lifecycle/disposal and reconnect consistency analysis, including `WV-VITE-01`, `WV-C03/WV-C06`, `WV-APP-01`, `WV-RESYNC-01`, `WV-TR-01`, component priorities `WV-C12`–`WV-C14`, hook priorities `WV-C15`–`WV-C16`, `WV-C17` and `WV-TEST-01`.
4. Verify the font binary internals when retrievable; retain the existing limits on PNG header checks and runtime asset loading.
5. Continue E2E, scripts, CI/release and system dependency-map work under their owning governance scopes.
6. Keep runtime, browser, VS Code host, build, lint, typecheck and test execution open until explicitly authorized and actually performed.

## Explicit limitations

- Static source and documentation investigation only.
- No tests, build, lint, typecheck, protocol generation or runtime experiments executed.
- No application source, assets, package manifest or configuration changed.
- Binary header verification does not establish full image/font integrity.
- The audit is not complete merely because all paths and SHAs reconcile.


## Latest continuation — GOV-WEBVIEW-010

- Static producer → consumer tracing is complete for all 34 ClientMessage and 46 ServerMessage variants. The detailed UI producer, host ingress, server producer, recipient/fan-out, validation and persistence map is owned by PROTOCOL-MATRIX.md.
- New static findings: WV-PROTO-01 (shared Directory-to-Area mappings are persisted without notifying already-connected peers) and WV-PROTO-02 (external asset directory reload is delivered only to the requesting client). Both are OPEN and conditional on multiple connected standalone clients; neither has been runtime reproduced.
- The five standalone-unhandled ClientMessage variants are explicitly reconciled with VS Code host actions/local browser behavior. focusAgent is sent by App in both runtimes; standalone intentionally no-ops the server-side action.
- Existing WV-C08 / server F-08 remain the canonical validation gap. Runtime schema enforcement, complete field-by-field authorization, and some shared-setting fan-out questions remain open.
- No source, test, build, package, asset or configuration files were modified; no tests/build/lint/typecheck/runtime experiments were executed.

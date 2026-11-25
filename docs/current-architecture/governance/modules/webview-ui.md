# Webview UI Module Audit Record

**Status:** IN PROGRESS — complete tracked-file inventory and SHA reconciliation; historical static-review coverage and cross-layer verification remain open.  
**Audit sessions:** `GOV-WEBVIEW-001` through `GOV-WEBVIEW-007` (2026-11-25)  
**Source baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Verification commit:** `8fc045de9ddae905cc131498d918ddbd64cc8bca`  
**Verification tree:** `6c65c2127b2fdf3742f76b317d43e9faccb61506` (`truncated: false`)  
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

At verification commit `8fc045de9ddae905cc131498d918ddbd64cc8bca`:

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
| `TEST-INVENTORIED` | 7 | Complete test source/assertions inspected in GOV-WEBVIEW-002/003; not executed |
| `FETCHED / INVENTORIED` | 47 | Test files fetched/inventoried; complete assertion review remains; not executed |
| `BINARY HEADER VERIFIED` | 143 | Header/signature/metadata checks only, as specified per file |
| `NOT VERIFIED` | 1 | `public/fonts/FSPixelSansUnicode-Regular.ttf` internals remain unavailable |

These categories sum to 459. GOV-WEBVIEW-005 directly reviewed the 32 component rows previously left as reported-review; together with the 22 component rows already REVIEWED, `src/components/` is 54/54 REVIEWED. GOV-WEBVIEW-006 directly reviewed the 14 remaining reported-review hook rows; together with the 10 previously REVIEWED hook rows, `src/hooks/` is 24/24 REVIEWED. All seven previously fetched/reported source rows elsewhere in webview source were directly reviewed in GOV-WEBVIEW-008; they were the same seven rows described in the earlier continuation note, not a second independent group. The 9 character UI rows are also directly reviewed. The remaining 47 test files are inventoried but still need assertion-level review. No tests were executed. Do not repeat completed work wholesale. The single unverified font must remain open until its binary content is actually available.

## Documentation verification

All ten existing webview architecture documents were fetched and confirmed non-empty at the verification commit: README, FILE-REFERENCE, DEEP-AUDIT, ASSET-INVENTORY, PROTOCOL-MATRIX, MODULE-OVERVIEW, STATE-MANAGEMENT, RENDERING-ENGINE, DEPENDENCIES and TRANSPORT.

The existing documentation remains the technical source for module details. This governance record consolidates status and ownership; it does not replace those documents.

## Findings and continuation

GOV-WEBVIEW-005 adds three static component observations for future technical prioritization: `WV-C12` (touch cancellation shares the touch-end completion path), `WV-C13` (modal dialog semantics and focus lifecycle need verification), and `WV-C14` (loading-art rejection has no local recovery path). GOV-WEBVIEW-006 adds `WV-C15` (editor keyboard shortcuts also receive events from text inputs) and `WV-C16` (Shift+Z redo key comparison is lowercase-only). GOV-WEBVIEW-007 completes the 9 character UI line-coverage gaps; no new finding ID was warranted. These are OPEN static findings, not runtime-reproduced defects or fixes. It also adds component-level lifecycle evidence to the existing `WV-C03/WV-C06` concern. These are OPEN static findings, not runtime-reproduced defects or fixes.

Preserve existing `WV-` findings and their states in [DEEP-AUDIT.md](../../webview-ui/DEEP-AUDIT.md). The reconnect trace in GOV-WEBVIEW-001 adds `WV-RESYNC-01`, a static finding about applying a server seat snapshot while the client still has its pre-reconnect layout. It remains open and is not runtime reproduced.

The historical SHA reconciliation for all 62 reported-review rows is complete. GOV-WEBVIEW-005 then directly re-fetched and reviewed the 32 component rows with missing per-file coverage evidence. All 54 files under `webview-ui/src/components/` are now REVIEWED (22 previously reviewed plus 32 reviewed in GOV-WEBVIEW-005). The remaining reported-review source scope is 7 other source rows; all 9 character UI rows are now REVIEWED. Hooks are complete at 24/24 REVIEWED.

The next work is:

1. Perform targeted direct source verification for the remaining 7 reported-review source rows elsewhere in webview-ui. Record exact line ranges and cross-references; promote only files whose complete content is verified.
2. Review the remaining 47 test files beyond inventory where needed; map assertions to implementation and keep execution separate.
3. Continue ClientMessage/ServerMessage producer-consumer tracing, field validation, authorization, persistence and ordering through UI, adapter and server.
4. Verify the font binary internals when retrievable; retain the existing limits on PNG header checks and runtime asset loading.
5. Continue host lifecycle/disposal and reconnect consistency analysis, including `WV-APP-01`, `WV-TR-01`, `WV-RESYNC-01`, `WV-VITE-01`, `WV-C03/WV-C06`, the component-level priorities `WV-C12`–`WV-C14`, hook priorities `WV-C15`–`WV-C16`, and the completed character UI review GOV-WEBVIEW-007.
6. Keep runtime, browser, VS Code host, build, lint, typecheck and test verification open until explicitly authorized and actually performed.

## Explicit limitations

- Static source and documentation investigation only.
- No tests, build, lint, typecheck, protocol generation or runtime experiments executed.
- No application source, assets, package manifest or configuration changed.
- Binary header verification does not establish full image/font integrity.
- The audit is not complete merely because all paths and SHAs reconcile.

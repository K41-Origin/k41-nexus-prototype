# Webview UI — Current Architecture

**Status:** IN PROGRESS — static source, test assertion, protocol producer-consumer, field-level contract and cross-layer lifecycle/reconnect reviews are complete for their recorded scopes. Runtime/cross-client verification and one font binary remain open.
**Historical baseline:** `4dd14d612fd37d89ffbbc977959f572395c4eec8`.
**Latest source verification:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated=false`. The 459/459 path/blob identities remain unchanged.

The governance module record is the consolidated status owner. This README is navigation and scope context; detailed evidence remains in the deep audit and file ledger.

## Audit records

- [Governance module record](../governance/modules/webview-ui.md)
- [Deep audit and findings](DEEP-AUDIT.md)
- [File audit ledger — all 459 tracked blobs](FILE-REFERENCE.md)
- [Control-plane protocol matrix](PROTOCOL-MATRIX.md)
- [Public asset metadata inventory](ASSET-INVENTORY.md)
- [Module overview](MODULE-OVERVIEW.md)
- [Rendering engine](RENDERING-ENGINE.md)
- [State management](STATE-MANAGEMENT.md)
- [Transport](TRANSPORT.md)
- [Dependencies](DEPENDENCIES.md)

## Current coverage

The 459 tracked blobs comprise 302 files under `src/`, 92 under `public/`, 54 test files and 11 root/package/build/config files.

The latest governance record reports:
- 261 source/config rows `REVIEWED`;
- 54 test files with assertion-level `TEST-INVENTORIED` review (not executed);
- 143 binary assets with header/signature-level verification only;
- 1 binary font (`public/fonts/FSPixelSansUnicode-Regular.ttf`) whose internals remain `NOT VERIFIED`;
- 34/34 ClientMessage and 46/46 ServerMessage variants statically traced;
- all 34 ClientMessage variants covered by field-level validation/authorization/persistence/side-effect review;
- cross-layer lifecycle, reconnect and state-reconciliation static trace recorded in GOV-WEBVIEW-012.

The former 118 reported-review rows and 54 inventory-only test rows are no longer open: source review and assertion-level review were completed in GOV-WEBVIEW-005–009. Do not repeat unchanged files.

## Remaining verification

- Runtime reconnect/resync, cross-client synchronization and state reconciliation for WV-RESYNC-01/02 and WV-PROTO-01/02/03.
- Runtime behavior and lifecycle verification for WV-APP-01 and WV-TR-01.
- Retrieval and internal inspection of the TTF font binary.
- Full binary decode/integrity and runtime asset loading; prior PNG checks establish signature/IHDR dimensions only, not full decode or CRC integrity.
- Browser accessibility/rendering and multi-client behavior.

No tests, build, lint, typecheck, browser/VS Code host or runtime experiments were executed in the recorded static audit. Do not mark the module complete until the open evidence is obtained and recorded.

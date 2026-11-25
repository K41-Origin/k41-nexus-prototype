# Webview UI — Current Architecture

**Status:** IN PROGRESS — inventory/SHA reconciliation complete; static content and cross-layer audit remain open.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8  
**Latest SHA reconciliation:** 8fc045de9ddae905cc131498d918ddbd64cc8bca — 459/459 ledger paths and blob SHAs match.

The audit is tracked against a fixed repository baseline. Do not repeat unchanged files already reviewed; consult the per-file blob-SHA ledger first.

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

## Current scope

The inventory contains 302 files under src/, 92 under public/, 54 tests and 11 root/package/build/config files. Previous work covered Office, terminal/touch, character/wardrobe, mobile, music, conversations, usage, transport and major application/component/hook areas. Coverage and outstanding verification are differentiated in FILE-REFERENCE.md and DEEP-AUDIT.md.

All 459 tracked paths and blob SHAs have been reconciled against the fixed baseline with no additions, removals or changes. This confirms inventory identity, not complete content review. The ledger still contains 118 reported-review rows requiring exact line-coverage reconciliation, 54 inventoried test files, and one unverified font.

The 60 public PNG files have signature and IHDR dimension records. The TTF font internals, complete asset pixel/CRC integrity, manifest references and protocol producer/consumer side-effect matrix remain open.

No application source has been modified. No tests, build, lint, typecheck or runtime experiments have been executed. Do not mark the module complete until every text file has defensible review coverage, binary assets have appropriate metadata/purpose records, and the control-plane protocol matrix is complete.

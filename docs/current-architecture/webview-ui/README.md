# Webview UI — Current Architecture

**Status:** IN PROGRESS — static audit not complete.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

The audit is tracked against a fixed repository baseline. Do not repeat unchanged files already reviewed; consult the per-file blob-SHA ledger first.

## Audit records

- [Deep audit and findings](DEEP-AUDIT.md) — subsystem architecture, data flows, findings, evidence limits and remaining work.
- [File audit ledger](FILE-REFERENCE.md) — all 459 tracked blobs with baseline SHA, size and conservative review status.
- [Module overview](MODULE-OVERVIEW.md)
- [Rendering engine](RENDERING-ENGINE.md)
- [State management](STATE-MANAGEMENT.md)
- [Transport](TRANSPORT.md)
- [Dependencies](DEPENDENCIES.md)

## Current scope

The inventory contains 302 files under src/, 92 under public/, 54 tests and 11 root/package/build/config files. Previous work has covered Office, terminal/touch, character/wardrobe, mobile, music, conversations, usage, transport and major application/component/hook areas. Coverage and outstanding verification are explicitly differentiated in DEEP-AUDIT.md and FILE-REFERENCE.md.

No application source has been modified. No tests, build, lint, typecheck or runtime experiments have been executed. The module must not be marked complete until every text file is fully reviewed, binary assets have defensible metadata/purpose records, and the control-plane protocol matrix is complete.

# Repository Audit Register

**Status snapshot:** 2026-11-25 — core, server and webview-ui inventories reconciled at `8fc045de9ddae905cc131498d918ddbd64cc8bca`.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Governance branch:** `docs/hybrid-llm-engine-direction`  
**Source branch for current-state audit:** `main` unless a module record explicitly declares another baseline.

This register is the consolidated index. Detailed findings remain in their owning module records; this file must not duplicate their full analysis.

## Branch and baseline policy

| Ref | Commit | Purpose |
|---|---|---|
| `main` | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | Current application source reference at governance initialization |
| `docs/hybrid-llm-engine-direction` | `8fc045de9ddae905cc131498d918ddbd64cc8bca` | Latest governance/documentation branch tip before this webview consolidation |
| Core source snapshot | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | Historical core inventory root; all 28 core blob SHAs reconciled |
| Server source snapshot | `f1af3e0c36573e4317143969cf43dde642877ba6` | Canonical server audit baseline; all 135 server blob SHAs reconciled |
| Webview UI source snapshot | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | Canonical webview audit baseline; all 459 webview blob SHAs reconciled at the governance branch tip |

The core, server and webview source blob identities have been reconciled at the stated snapshots. This does not imply runtime correctness, test success, or that divergent branch histories have been merged.

## Module status

| Module / scope | Status | Evidence owner | Next action |
|---|---|---|---|
| `core/` | **AUDITED — 28/28 files individually re-fetched; SHA and file-reference reconciliation complete** | [Core audit record](modules/core.md), [core file reference](../core/FILE-REFERENCE.md) | Cross-layer consumer/runtime verification remains open |
| `server/` | **AUDITED — 135/135 tracked files reconciled; source SHAs unchanged; 9/9 architecture docs fetched** | [Server governance record](modules/server.md), [deep audit](../server/DEEP-AUDIT.md), [file reference](../server/FILE-REFERENCE.md) | Preserve F-01–F-08; runtime/test verification remains open |
| `webview-ui/` | **IN PROGRESS — 459/459 paths/SHA reconciled; 245 ledger rows REVIEWED; 16 reported-review source rows, 47 inventoried-only tests and binary evidence remain open; components 54/54 and hooks 24/24 REVIEWED** | [Webview governance record](modules/webview-ui.md), [deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Continue direct source verification of the remaining 16 reported-review rows (9 character UI files, 7 other source files), then 47 test assertion reviews, protocol tracing and binary/font evidence |
| `adapters/` | **NOT AUDITED as a module** | Pending | Audit host lifecycle, ownership, activation/deactivation, protocol bridges and disposal |
| `e2e/` | **INVENTORY / SAMPLING PENDING** | Preliminary audit and tree inventory | Review fixtures, test assertions, host workflows and coverage gaps |
| `scripts/` | **INVENTORY / IMPLEMENTATION REVIEW PENDING** | Preliminary audit and tree inventory | Review generation, build, packaging, validation and release side effects |
| `.github/` and CI/release | **REVIEW PENDING** | Preliminary audit | Inspect workflow permissions, triggers, secrets, artifact provenance and release process |
| Repository docs / ADRs | **PARTIAL** | Preliminary audit and module docs | Read remaining authoritative decisions before architecture proposals |
| System dependency map | **NOT AUDITED** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md) | Build evidence-backed import/runtime/lifecycle edges after module audits |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. Continue `webview-ui/` from the 16 remaining reported-review source rows (9 character UI files and 7 other source files), reconnect/seat reconciliation, protocol, test-inventory and binary gaps. The 54 component files and 24 hooks are fully source-reviewed; do not repeat them without changed SHAs or a new cross-module question.
2. Audit `adapters/` as the next major module, correlating it with completed core/server/webview records.
3. Review test suites and scripts as evidence of intended behavior, not as proof of correctness until executed.
4. Build the system dependency/lifecycle map from source references and runtime observations.
5. Run targeted tests/build/runtime experiments only when authorized and record exact commands/results.

## Finding ID namespaces

| Prefix | Owning scope |
|---|---|
| `CORE-` | Core source/contracts |
| `F-` | Existing server findings |
| `WV-` | Webview UI findings |
| `ADP-` | Adapter findings (reserved) |
| `E2E-` | End-to-end test/coverage findings (reserved) |
| `BUILD-` | Scripts, CI, packaging and supply chain (reserved) |

IDs are stable. Do not recycle a closed or rejected finding ID.

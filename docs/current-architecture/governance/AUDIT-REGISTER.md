# Repository Audit Register

**Status snapshot:** 2026-11-25 (core and server inventory reconciled at `d571b69d03b6993ab14d66f0d11d5a3d79a52a5b`)  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Governance branch:** `docs/hybrid-llm-engine-direction`  
**Source branch for current-state audit:** `main` unless a module record explicitly declares another baseline.

This register is the consolidated index. Detailed findings remain in their owning module records; this file must not duplicate their full analysis.

## Branch and baseline policy

| Ref | Commit | Purpose |
|---|---|---|
| `main` | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | Current application source reference at governance initialization |
| `docs/hybrid-llm-engine-direction` | `13354f1a74b75b9ab3d8982544414134b1940ae3` | Audit documentation and governance record branch |
| Core source snapshot | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | Historical inventory root; all 28 core blob SHAs individually re-fetched and matched at governance branch tip `c522dbdac525b226ec84afa5b7c829289ea1949f` |

The core source blob SHAs at the governance branch tip match the recorded core audit snapshot. This confirms source identity for `core/` at that branch tip; it does not imply that the two branch histories have been fully walked or merged.

## Module status

| Module / scope | Status | Evidence owner | Next action |
|---|---|---|---|
| `core/` | **AUDITED — 28/28 files individually re-fetched; SHA and file-reference reconciliation complete** | [Core audit record](modules/core.md), [core file reference](../core/FILE-REFERENCE.md) | Cross-layer consumer/runtime verification remains open |
| `server/` | **AUDITED — 135/135 tracked files reconciled; source SHAs unchanged; 9/9 architecture docs fetched** | [Server governance record](modules/server.md), [deep audit](../server/DEEP-AUDIT.md), [file reference](../server/FILE-REFERENCE.md) | Preserve F-01–F-08; runtime/test verification remains open |
| `webview-ui/` | **IN PROGRESS** | [Webview deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Continue from SHA reconciliation and documented cross-module gaps; do not repeat reviewed files |
| `adapters/` | **NOT AUDITED as a module** | Pending | Audit host lifecycle, ownership, activation/deactivation, protocol bridges and disposal |
| `e2e/` | **INVENTORY / SAMPLING PENDING** | Preliminary audit and tree inventory | Review fixtures, test assertions, host workflows and coverage gaps |
| `scripts/` | **INVENTORY / IMPLEMENTATION REVIEW PENDING** | Preliminary audit and tree inventory | Review generation, build, packaging, validation and release side effects |
| `.github/` and CI/release | **REVIEW PENDING** | Preliminary audit | Inspect workflow permissions, triggers, secrets, artifact provenance and release process |
| Repository docs / ADRs | **PARTIAL** | Preliminary audit and module docs | Read remaining authoritative decisions before architecture proposals |
| System dependency map | **NOT AUDITED** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md) | Build evidence-backed import/runtime/lifecycle edges after module audits |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. Finish `webview-ui/` without repeating unchanged reviewed files.
2. Audit `adapters/` as the next major module, correlating it with completed server/core records.
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

# Repository Audit Register

**Status snapshot:** 2026-11-25 — governance continuity update; source branch `main` currently points to `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; module baselines remain individually scoped below.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Governance branch:** `docs/hybrid-llm-engine-direction`  
**Source branch for current-state audit:** `main` unless a module record explicitly declares another baseline.

This register is the consolidated index. Detailed findings remain in their owning module records; this file must not duplicate their full analysis.

## Branch and baseline policy

| Ref | Commit | Purpose |
|---|---|---|
| `main` | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | Current application source reference at governance initialization |
| `docs/hybrid-llm-engine-direction` | `e9bce038d8cf7d32640148ebacb97c49298fe796` | Governance branch tip immediately before this continuity update |
| Core source snapshot | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | Historical core inventory root; all 28 core blob SHAs reconciled |
| Server source snapshot | `f1af3e0c36573e4317143969cf43dde642877ba6` | Canonical server audit baseline; all 135 server blob SHAs reconciled |
| Webview UI source snapshot | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | Canonical webview audit baseline; all 459 webview blob SHAs reconciled at the governance branch tip |

The core, server and webview source blob identities have been reconciled at the stated snapshots. This does not imply runtime correctness, test success, or that divergent branch histories have been merged.

## Module status

| Module / scope | Status | Evidence owner | Next action |
|---|---|---|---|
| `core/` | **AUDITED — 28/28 files individually re-fetched; SHA and file-reference reconciliation complete** | [Core audit record](modules/core.md), [core file reference](../core/FILE-REFERENCE.md) | Cross-layer consumer/runtime verification remains open |
| `server/` | **AUDITED — 135/135 tracked files reconciled; source SHAs unchanged; 9/9 architecture docs fetched** | [Server governance record](modules/server.md), [deep audit](../server/DEEP-AUDIT.md), [file reference](../server/FILE-REFERENCE.md) | Preserve F-01–F-08; runtime/test verification remains open |
| `webview-ui/` | **IN PROGRESS — 459/459 paths/SHA reconciled; 261 REVIEWED; 0 reported-review source rows; 7 TEST-INVENTORIED; 47 FETCHED / INVENTORIED; 143 BINARY HEADER VERIFIED; 1 NOT VERIFIED** | [Webview governance record](modules/webview-ui.md), [deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Assertion-level review of the 47 inventoried-only tests; then protocol tracing, lifecycle/reconnect and binary/font evidence |
| `adapters/` | **STATIC AUDIT COMPLETE — 8/8 files reviewed; findings A-01–A-10 documented; dynamic verification open** | [Adapters governance record](modules/adapters.md), [deep audit](../adapters/DEEP-AUDIT.md), [file reference](../adapters/FILE-REFERENCE.md) | Reconcile 8 file SHAs against active source tree before any revisit; then targeted VS Code lifecycle/activation/disposal verification when authorized |
| `e2e/` | **INVENTORY / SAMPLING PENDING** | Preliminary audit and tree inventory | Review fixtures, test assertions, host workflows and coverage gaps |
| `scripts/` | **INVENTORY / IMPLEMENTATION REVIEW PENDING** | Preliminary audit and tree inventory | Review generation, build, packaging, validation and release side effects |
| `.github/` and CI/release | **REVIEW PENDING** | Preliminary audit | Inspect workflow permissions, triggers, secrets, artifact provenance and release process |
| Repository docs / ADRs | **PARTIAL** | Preliminary audit and module docs | Read remaining authoritative decisions before architecture proposals |
| System dependency map | **NOT AUDITED** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md) | Build evidence-backed import/runtime/lifecycle edges after module audits |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. `webview-ui/`: review the 47 `FETCHED / INVENTORIED` test files at assertion level (7 other test files are already `TEST-INVENTORIED`); do not reread the 261 `REVIEWED` source rows unless their blob SHA changes or a concrete cross-layer question requires it.
2. `webview-ui/`: continue ClientMessage/ServerMessage producer-consumer tracing across UI → adapter → server, including runtime validation, authorization, side effects, persistence and ordering. Reuse existing `WV-APP-01`, `WV-C08`, `WV-RESYNC-01`, `WV-TR-01` evidence; add only missing edges.
3. `webview-ui/` + `adapters/` + `server/`: continue lifecycle/disposal/reconnect analysis. Adapter static audit is already complete (8/8 files; A-01–A-10); do not restart it. Reconcile adapter SHAs first and inspect only changed files or unresolved lifecycle edges.
4. `webview-ui/`: retrieve/verify the font binary internals when available. Existing 143 PNG/assets are header/signature-level only; do not label them fully decoded or runtime-validated.
5. `e2e/`, `scripts/`, `.github/` and CI/release remain unaudited or partially inventoried; begin only after the relevant governance/module records are read.
6. Build the system dependency/lifecycle map from evidence-backed imports, producers/consumers and ownership edges.
7. Tests/build/typecheck/lint/runtime experiments remain unexecuted unless explicitly authorized; record exact commands, environment and results if later authorized.

## Finding ID namespaces

| Prefix | Owning scope |
|---|---|
| `CORE-` | Core source/contracts |
| `F-` | Existing server findings |
| `WV-` | Webview UI findings |
| `ADP-` / `A-` | Adapter findings; existing deep-audit IDs A-01–A-10 are stable and must not be reused |
| `E2E-` | End-to-end test/coverage findings (reserved) |
| `BUILD-` | Scripts, CI, packaging and supply chain (reserved) |

IDs are stable. Do not recycle a closed or rejected finding ID.

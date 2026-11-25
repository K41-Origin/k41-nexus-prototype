# Repository Audit Register

**Status snapshot:** 2026-11-25 — GOV-WEBVIEW-012 static cross-layer lifecycle/reconnect/state-reconciliation trace consolidated; source branch `main` points to `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; module baselines remain individually scoped below.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Governance branch:** `docs/hybrid-llm-engine-direction`  
**Source branch for current-state audit:** `main` unless a module record explicitly declares another baseline.

This register is the consolidated index. Detailed findings remain in their owning module records; this file must not duplicate their full analysis.

## Branch and baseline policy

| Ref | Commit | Purpose |
|---|---|---|
| `main` | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | Current application source reference at governance initialization |
| `docs/hybrid-llm-engine-direction` | `189e28970ec5d05abd536a57a4adab53399c27fd` | Historical governance branch tip before GOV-WEBVIEW-010 publication |
| `docs/hybrid-llm-engine-direction` | `08ee7fc7edddc358ba883cb4d25d6282becd10a8` | Governance branch tip verified before GOV-WEBVIEW-012 publication |
| Core source snapshot | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | Historical core inventory root; all 28 core blob SHAs reconciled |
| Server source snapshot | `f1af3e0c36573e4317143969cf43dde642877ba6` | Canonical server audit baseline; all 135 server blob SHAs reconciled |
| Webview UI source snapshot | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | Canonical webview audit baseline; all 459 webview blob SHAs reconciled at the governance branch tip |
| Adapter source verification | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | 8/8 adapter path/blob SHAs match historical baseline `4dd14d612fd37d89ffbbc977959f572395c4eec8` |

The core, server and webview source blob identities have been reconciled at the stated snapshots. This does not imply runtime correctness, test success, or that divergent branch histories have been merged.

## Module status

| Module / scope | Status | Evidence owner | Next action |
|---|---|---|---|
| `core/` | **AUDITED — 28/28 files individually re-fetched; SHA and file-reference reconciliation complete** | [Core audit record](modules/core.md), [core file reference](../core/FILE-REFERENCE.md) | Cross-layer consumer/runtime verification remains open |
| `server/` | **AUDITED — 135/135 tracked files reconciled; source SHAs unchanged; 9/9 architecture docs fetched** | [Server governance record](modules/server.md), [deep audit](../server/DEEP-AUDIT.md), [file reference](../server/FILE-REFERENCE.md) | Preserve F-01–F-09; runtime/test verification remains open |
| `webview-ui/` | **IN PROGRESS — 459/459 paths/SHA reconciled; 261 REVIEWED; 54 TEST-INVENTORIED; 0 FETCHED / INVENTORIED; 143 BINARY HEADER VERIFIED; 1 NOT VERIFIED; 34/34 ClientMessage and 46/46 ServerMessage variants statically traced; field-level validation/authorization/persistence/side-effect audit and cross-layer lifecycle/reconnect static trace complete** | [Webview governance record](modules/webview-ui.md), [deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Runtime verification of WV-RESYNC-01/02, WV-APP-01, WV-TR-01 and WV-PROTO-01/02/03 (authorization required); binary/font evidence and remaining cross-client questions |
| `adapters/` | **STATIC AUDIT COMPLETE — 8/8 files reviewed and SHA-reconciled against main@56f354f; findings A-01–A-10 documented; dynamic verification open** | [Adapters governance record](modules/adapters.md), [deep audit](../adapters/DEEP-AUDIT.md), [file reference](../adapters/FILE-REFERENCE.md) | Continue targeted cross-module lifecycle/activation/disposal verification when authorized; do not repeat unchanged adapter source review |
| `e2e/` | **IN PROGRESS — 47/47 blobs TREE-VERIFIED/SHA-RECONCILED/fetched; 21/21 specs / 152 assertions TEST-INVENTORIED; 25/25 harness files SOURCE-VERIFIED; E2E-001–E2E-009 OPEN** | [E2E governance record](modules/e2e.md), [deep audit](../e2e/DEEP-AUDIT.md), [file ledger](../e2e/FILE-REFERENCE.md) | Harness and targeted cross-module static review complete (GOV-E2E-003/004); continue scripts/CI; preserve E2E-001–E2E-009 |
| `scripts/` | **INVENTORY / IMPLEMENTATION REVIEW PENDING** | Preliminary audit and tree inventory | Review generation, build, packaging, validation and release side effects |
| `.github/` and CI/release | **REVIEW PENDING** | Preliminary audit | Inspect workflow permissions, triggers, secrets, artifact provenance and release process |
| Repository docs / ADRs | **PARTIAL** | Preliminary audit and module docs | Read remaining authoritative decisions before architecture proposals |
| System dependency map | **NOT AUDITED** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md) | Build evidence-backed import/runtime/lifecycle edges after module audits |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. `webview-ui/`: all 54 test files now have assertion-level inventory/review evidence (`TEST-INVENTORIED`); 0 remain `FETCHED / INVENTORIED`. Do not reread the 261 `REVIEWED` source rows unless their blob SHA changes or a concrete cross-layer question requires it. Preserve test-quality finding `WV-TEST-01`.
2. `webview-ui/`: static ClientMessage/ServerMessage producer-consumer tracing is complete (34/34 and 46/46 variants). Per-field validation/authorization/persistence/side-effect audit is complete for all 34 ClientMessage variants (GOV-WEBVIEW-011). Preserve `WV-C08` / `F-08` and `WV-PROTO-01/02/03`; continue lifecycle/reconnect and cross-client consistency without repeating the completed matrix.
3. `webview-ui/` + `adapters/` + `server/`: static cross-layer lifecycle/reconnect/state-reconciliation trace is recorded in GOV-WEBVIEW-012. Preserve WV-RESYNC-01/02, WV-APP-01, WV-TR-01, A-03/A-05/A-06 and F-01/F-03/F-04/F-05/F-09. Next work is design-contract clarification and authorized runtime verification; do not restart unchanged module reviews.
4. `webview-ui/`: retrieve/verify the font binary internals when available. Existing 143 PNG/assets are header/signature-level only; do not label them fully decoded or runtime-validated.
5. `e2e/`: complete tree/SHA inventory (GOV-E2E-001), assertion-level review (GOV-E2E-002, 21/21 specs, 152 tests), and full static fixture/helper/setup/config review (GOV-E2E-003, 25/25 files) are complete. Preserve E2E-001–E2E-008. Targeted lifecycle/hook/reconnect/persistence/test-hook trace is complete (GOV-E2E-004); continue `scripts/`, `.github/` and CI/release under their owning scopes.
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

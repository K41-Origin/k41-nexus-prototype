# Repository Audit Register

**Status snapshot:** 2026-11-25 — governance reconciliation session GOV-DOCGOV-001. Documentation branch HEAD at session start: `02b3118edf38713cc55b581a916fd61dea8bfd1b`; source branch `main`: `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. Complete recursive tree comparison: 739/739 source paths and blob SHAs are identical between `main` and the documentation branch snapshot; 61 additional documentation blobs exist on the documentation branch. Module baselines remain individually scoped below.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Governance branch:** `docs/hybrid-llm-engine-direction`  
**Source branch for current-state audit:** `main` unless a module record explicitly declares another baseline.

This register is the consolidated index. Detailed findings remain in their owning module records; this file must not duplicate their full analysis.

## Branch and baseline policy

| Ref | Commit | Purpose |
|---|---|---|
| `main` | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | Current application source reference at governance initialization |
| `docs/hybrid-llm-engine-direction` | `189e28970ec5d05abd536a57a4adab53399c27fd` | Historical governance branch tip before GOV-WEBVIEW-010 publication |
| `docs/hybrid-llm-engine-direction` | `08ee7fc7edddc358ba883cb4d25d6282becd10a8` | Historical governance branch tip verified before GOV-WEBVIEW-012 publication |
| `docs/hybrid-llm-engine-direction` | `02b3118edf38713cc55b581a916fd61dea8bfd1b` | Actual documentation branch HEAD verified at start of GOV-DOCGOV-001; subsequent reconciliation commits are recorded in the evidence log |
| Core source snapshot | `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1` | Historical core inventory root; all 28 core blob SHAs reconciled |
| Server source snapshot | `f1af3e0c36573e4317143969cf43dde642877ba6` | Canonical server audit baseline; all 135 server blob SHAs reconciled |
| Webview UI source snapshot | `4dd14d612fd37d89ffbbc977959f572395c4eec8` | Canonical webview audit baseline; all 459 webview blob SHAs reconciled at the governance branch tip |
| Adapter source verification | `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` | 8/8 adapter path/blob SHAs match historical baseline `4dd14d612fd37d89ffbbc977959f572395c4eec8` |

The full `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` tree (856 entries, 739 blobs, 117 trees, `truncated=false`) was compared against documentation branch snapshot `02b3118edf38713cc55b581a916fd61dea8bfd1b`: all 739 source paths exist on both branches with identical blob SHAs; no source additions, removals or content differences were found. The documentation branch contains 61 additional blobs. This verifies source snapshot identity, not runtime correctness, test success, or branch-history equivalence.

## Module status

| Module / scope | Status | Evidence owner | Next action |
|---|---|---|---|
| `core/` | **AUDITED — 28/28 files individually re-fetched; SHA and file-reference reconciliation complete** | [Core audit record](modules/core.md), [core file reference](../core/FILE-REFERENCE.md) | Cross-layer consumer/runtime verification remains open |
| `server/` | **AUDITED — 135/135 tracked files reconciled; source SHAs unchanged; 9/9 architecture docs fetched** | [Server governance record](modules/server.md), [deep audit](../server/DEEP-AUDIT.md), [file reference](../server/FILE-REFERENCE.md) | Preserve F-01–F-09; runtime/test verification remains open |
| `webview-ui/` | **IN PROGRESS — 459/459 paths/SHA reconciled; 261 REVIEWED; 54 TEST-INVENTORIED; 0 FETCHED / INVENTORIED; 143 BINARY HEADER VERIFIED; 1 NOT VERIFIED; 34/34 ClientMessage and 46/46 ServerMessage variants statically traced; field-level validation/authorization/persistence/side-effect audit and cross-layer lifecycle/reconnect static trace complete** | [Webview governance record](modules/webview-ui.md), [deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Runtime verification of WV-RESYNC-01/02, WV-APP-01, WV-TR-01 and WV-PROTO-01/02/03 (authorization required); binary/font evidence and remaining cross-client questions |
| `adapters/` | **STATIC AUDIT COMPLETE — 8/8 files reviewed and SHA-reconciled against main@56f354f; findings A-01–A-10 documented; dynamic verification open** | [Adapters governance record](modules/adapters.md), [deep audit](../adapters/DEEP-AUDIT.md), [file reference](../adapters/FILE-REFERENCE.md) | Continue targeted cross-module lifecycle/activation/disposal verification when authorized; do not repeat unchanged adapter source review |
| `e2e/` | **IN PROGRESS — 47/47 blobs TREE-VERIFIED/SHA-RECONCILED/fetched; 21/21 specs / 152 assertions TEST-INVENTORIED; 25/25 harness files SOURCE-VERIFIED; E2E-001–E2E-009 OPEN** | [E2E governance record](modules/e2e.md), [deep audit](../e2e/DEEP-AUDIT.md), [file ledger](../e2e/FILE-REFERENCE.md) | Harness and targeted cross-module static review complete (GOV-E2E-003/004); preserve E2E-001–E2E-009; runtime/test execution remains open and requires authorization |
| `scripts/` | **STATIC AUDIT COMPLETE — 14/14 blobs TREE-VERIFIED, SHA-RECONCILED, individually fetched and SOURCE-VERIFIED; BUILD-001–BUILD-007 OPEN** | [Scripts governance record](modules/scripts.md), [deep audit](../SCRIPTS-AUDIT.md), [file reference](../SCRIPTS-FILE-REFERENCE.md) | Preserve BUILD-001–BUILD-007; .github/ and CI/release static audit is complete (10/10; BUILD-008–BUILD-013 OPEN) |
| `.github/` and CI/release | **STATIC AUDIT COMPLETE — historical 10/10 inventory; BUILD-008/009 remediation implemented on `security/remediate-build-008-009` (not merged; OPEN pending verification); BUILD-010–BUILD-013 OPEN** | [GitHub CI/release governance record](modules/github-ci.md), [deep audit](../github-ci/DEEP-AUDIT.md), [file reference](../github-ci/FILE-REFERENCE.md) | Review/merge remediation branch; run PR/manual negative tests and controlled release rehearsal; verify npm-publish Environment, trusted publisher, token scopes and organization rules |
| Repository docs / ADRs | **PARTIAL — three ADRs statically reconciled; Hybrid LLM Engine direction compared with current implementation; 12 future design decisions OPEN** | [Architecture/ADR reconciliation](../ARCHITECTURE-ADR-RECONCILIATION.md), [module record](modules/architecture-adr.md), [direction note](../../architecture/K41-HYBRID-LLM-ENGINE-DIRECTION.md) | Resolve requirements, threat model and 12 design questions before specification freeze; do not infer future engine features from current Pixel Agents code |
| System dependency map | **STATIC MAP IN PROGRESS — major compile, host composition, protocol, persistence, provider, transport and trust edges SOURCE-VERIFIED; runtime/transitive graph OPEN** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md), [module record](modules/system-dependency-map.md) | Complete transitive import/call graph and authorized runtime lifecycle verification; reuse existing module findings |
| Infrastructure (build/dependencies/testing/integrated CI) | **PARTIAL — build/package and test/CI static maps complete; direct dependency classes mapped; transitive/bundle/runtime verification OPEN** | [Infrastructure index](../infrastructure/README.md), [module record](modules/infrastructure.md), [build](../infrastructure/BUILD-SYSTEM.md), [dependencies](../infrastructure/DEPENDENCIES.md), [testing](../infrastructure/TESTING.md), [CI/CD](../infrastructure/CI-CD.md) | Verify bundle/tarball/VSIX outputs, transitive dependency/SBOM and authorized test/build matrix; do not repeat scripts or `.github/` static reviews |
| Architecture docs / ADR reconciliation | **PARTIAL** — architecture direction and three ADRs read; implementation conformance and unresolved decisions remain open | [Architecture direction](../../architecture/K41-HYBRID-LLM-ENGINE-DIRECTION.md), `docs/adr/` | Trace decisions to source contracts; resolve 12 open hybrid-engine design questions before specification freeze |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. `webview-ui/`: all 54 test files now have assertion-level inventory/review evidence (`TEST-INVENTORIED`); 0 remain `FETCHED / INVENTORIED`. Do not reread the 261 `REVIEWED` source rows unless their blob SHA changes or a concrete cross-layer question requires it. Preserve test-quality finding `WV-TEST-01`.
2. `webview-ui/`: static ClientMessage/ServerMessage producer-consumer tracing is complete (34/34 and 46/46 variants). Per-field validation/authorization/persistence/side-effect audit is complete for all 34 ClientMessage variants (GOV-WEBVIEW-011). Preserve `WV-C08` / `F-08` and `WV-PROTO-01/02/03`; continue lifecycle/reconnect and cross-client consistency without repeating the completed matrix.
3. `webview-ui/` + `adapters/` + `server/`: static cross-layer lifecycle/reconnect/state-reconciliation trace is recorded in GOV-WEBVIEW-012. Preserve WV-RESYNC-01/02, WV-APP-01, WV-TR-01, A-03/A-05/A-06 and F-01/F-03/F-04/F-05/F-09. Next work is design-contract clarification and authorized runtime verification; do not restart unchanged module reviews.
4. `webview-ui/`: retrieve/verify the font binary internals when available. Existing 143 PNG/assets are header/signature-level only; do not label them fully decoded or runtime-validated.
5. `e2e/`: complete tree/SHA inventory (GOV-E2E-001), assertion-level review (GOV-E2E-002, 21/21 specs, 152 tests), and full static fixture/helper/setup/config review (GOV-E2E-003, 25/25 files) are complete. Preserve E2E-001–E2E-009. Targeted lifecycle/hook/reconnect/persistence/test-hook trace is complete (GOV-E2E-004); scripts static audit is complete (14/14; BUILD-001–BUILD-007 OPEN); `.github/` inventory and trust-boundary design review are complete (GOV-GHCI-001/002); BUILD-008/009 remediation is implemented on a separate unmerged branch and remains OPEN pending verification; BUILD-010–BUILD-013 remain OPEN. Do not restart without changed source or a named verification gap.
6. Build the system dependency/lifecycle map from evidence-backed imports, producers/consumers and ownership edges; the map document now defines edge evidence and ownership but the map itself remains NOT AUDITED.
7. Complete infrastructure verification: transitive dependency/SBOM, bundle/package outputs and authorized build/test matrix; scoped `scripts/` and `.github/` reviews are complete and must not be repeated.
8. Continue architecture/ADR work from the published reconciliation; the three ADRs are statically reconciled and the 12 future hybrid-engine decisions remain open.
9. Tests/build/typecheck/lint/runtime experiments remain unexecuted unless explicitly authorized; record exact commands, environment and results if later authorized.

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


### BUILD-008/009 remediation snapshot

- Remediation branch: `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58` (based on `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`).
- BUILD-008: Vercel PR preview deployment jobs removed from `ci.yml`; preview functionality is suspended until a separately trusted artifact consumer is designed and verified.
- BUILD-009: manual dispatch is verification-only; publisher jobs run only for published releases and consume the build artifact without checkout/source execution. npm OIDC permission is isolated to its publisher job.
- Both findings remain OPEN. Verification gates and limitations are owned by [GitHub CI deep audit §10](../github-ci/DEEP-AUDIT.md).

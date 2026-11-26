# Repository Audit Register

**Status snapshot:** 2026-11-26 — GOV-WEBVIEW-CLOSE-001 and GOV-DEPMAP-003. Latest verified canonical `main` HEAD/tree: `f6464594f950b9af49274b8e1724e4d3e96c0271`; recursive tree 933 entries, `truncated=false`. Historical module baselines remain individually scoped below.  
**Repository:** `K41-Origin/k41-nexus-prototype`
**Latest source snapshots:** `main@b1104a808cf70382c2bd14a94d372d907a500cda` (933 tree entries, `truncated=false`); workflow remediation `security/remediate-build-008-009@1b71acd07b48d56527d0ad72e4d5e23713b390a7`. See GOV-INFRA-001, GOV-DEPMAP-001, GOV-ADR-001 and GOV-GAP-CLOSURE-001 in the evidence log.  
**Governance publication branch:** `docs/hybrid-llm-engine-direction` (historical; PR #1 merged); canonical branch: `main`.  
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
| `webview-ui/` | **AUDIT CLOSED — STATIC FILE AUDIT COMPLETE; 459/459 paths/SHA reconciled; 261 REVIEWED; 54 TEST-INVENTORIED; 144 CLOSED BY SCOPE ACCEPTANCE (143 binary display assets + 1 TTF); no file-level audit gaps** | [Webview governance record](modules/webview-ui.md), [deep audit](../webview-ui/DEEP-AUDIT.md), [file ledger](../webview-ui/FILE-REFERENCE.md) | Preserve existing WV findings; runtime/cross-client verification is separate follow-up and does not reopen file audit unless source identity or scope changes |
| `adapters/` | **STATIC AUDIT COMPLETE — 8/8 files reviewed and SHA-reconciled against main@56f354f; findings A-01–A-10 documented; dynamic verification open** | [Adapters governance record](modules/adapters.md), [deep audit](../adapters/DEEP-AUDIT.md), [file reference](../adapters/FILE-REFERENCE.md) | Continue targeted cross-module lifecycle/activation/disposal verification when authorized; do not repeat unchanged adapter source review |
| `e2e/` | **IN PROGRESS — 47/47 blobs TREE-VERIFIED/SHA-RECONCILED/fetched; 21/21 specs / 152 assertions TEST-INVENTORIED; 25/25 harness files SOURCE-VERIFIED; E2E-001–E2E-009 OPEN** | [E2E governance record](modules/e2e.md), [deep audit](../e2e/DEEP-AUDIT.md), [file ledger](../e2e/FILE-REFERENCE.md) | Harness and targeted cross-module static review complete (GOV-E2E-003/004); preserve E2E-001–E2E-009; runtime/test execution remains open and requires authorization |
| `scripts/` | **STATIC AUDIT COMPLETE — 14/14 blobs TREE-VERIFIED, SHA-RECONCILED, individually fetched and SOURCE-VERIFIED; BUILD-001–BUILD-007 OPEN** | [Scripts governance record](modules/scripts.md), [deep audit](../SCRIPTS-AUDIT.md), [file reference](../SCRIPTS-FILE-REFERENCE.md) | Preserve BUILD-001–BUILD-007; .github/ and CI/release static audit is complete (10/10; BUILD-008–BUILD-014 OPEN) |
| `.github/` and CI/release | **STATIC AUDIT COMPLETE — historical 10/10 inventory; BUILD-008/009 remediation implemented on `security/remediate-build-008-009` (not merged; OPEN pending verification); BUILD-010–BUILD-014 OPEN** | [GitHub CI/release governance record](modules/github-ci.md), [deep audit](../github-ci/DEEP-AUDIT.md), [file reference](../github-ci/FILE-REFERENCE.md) | Resolve BUILD-014 before merging the remediation PR; then run BUILD-008/009 negative/positive workflow verification, controlled release rehearsal, and verify npm-publish Environment, trusted publisher, token scopes and organization rules |
| Repository docs / ADRs | **PARTIAL — three ADRs statically reconciled; Hybrid LLM Engine direction compared with current implementation; 12 future design decisions OPEN** | [Architecture/ADR reconciliation](../ARCHITECTURE-ADR-RECONCILIATION.md), [module record](modules/architecture-adr.md), [direction note](../../architecture/K41-HYBRID-LLM-ENGINE-DIRECTION.md) | Resolve requirements, threat model and 12 design questions before specification freeze; do not infer future engine features from current Pixel Agents code |
| System dependency map | **STATIC MAP IN PROGRESS — npm lockfile transitive closure mapped; source import graph statically inventoried across 334 modules (GOV-DEPMAP-003); terminal/process ownership statically mapped (GOV-DEPMAP-004); bundle comparison, symbol-level call graph and runtime lifecycle OPEN** | [System dependency map](../SYSTEM-DEPENDENCY-MAP.md), [module record](modules/system-dependency-map.md) | Compare source imports with esbuild/Vite outputs and package artifacts; preserve runtime lifecycle and external-settings gaps |
| Infrastructure (build/dependencies/testing/integrated CI) | **PARTIAL — build/package and test/CI static maps complete; direct and transitive npm lockfile graph mapped; source import reachability, bundle/tarball/VSIX and runtime verification OPEN** | [Infrastructure index](../infrastructure/README.md), [module record](modules/infrastructure.md), [build](../infrastructure/BUILD-SYSTEM.md), [dependencies](../infrastructure/DEPENDENCIES.md), [testing](../infrastructure/TESTING.md), [CI/CD](../infrastructure/CI-CD.md) | Complete source import/call graph and bundle/metafile comparison; verify tarball/VSIX, SBOM and authorized test/build matrix; do not repeat scripts or `.github/` static reviews |

**Status meanings:** AUDITED is limited to the explicitly stated evidence scope. It does not imply runtime correctness, security certification, tests passing, or absence of defects.

## Current open workstreams

1. `webview-ui/`: file audit is CLOSED (459/459; 261 REVIEWED, 54 TEST-INVENTORIED, 144 CLOSED BY SCOPE ACCEPTANCE). Preserve `WV-TEST-01` and all other findings. Runtime/test execution is separate follow-up, not a remaining file-audit gap.
2. `webview-ui/`: 34/34 ClientMessage and 46/46 ServerMessage producer-consumer tracing plus all 34 field-level validation/authorization/persistence/side-effect review are complete. Preserve `WV-C08` / `F-08` and `WV-PROTO-01/02/03`; any runtime/cross-client work is separate and must not restart completed file reviews.
3. `webview-ui/` + `adapters/` + `server/`: static cross-layer lifecycle/reconnect/state-reconciliation trace is recorded in GOV-WEBVIEW-012. Preserve WV-RESYNC-01/02, WV-APP-01, WV-TR-01, A-03/A-05/A-06 and F-01/F-03/F-04/F-05/F-09. Next work is design-contract clarification and authorized runtime verification; do not restart unchanged module reviews.
4. `webview-ui/`: the TTF and all 143 binary display assets are CLOSED BY SCOPE ACCEPTANCE (2026-11-26), not complete technical validation. The 143 assets are accepted as visual/presentation-only; full pixel/entropy decode and browser/runtime rendering are outside the accepted scope and are not claimed. Existing PNG CRC/structure, ICO directory/embedded PNG CRC and JPEG marker/dimension evidence remains recorded. Reopen only if pixel integrity, provenance, visual fidelity/accessibility or runtime rendering becomes an explicit requirement.
5. `e2e/`: complete tree/SHA inventory (GOV-E2E-001), assertion-level review (GOV-E2E-002, 21/21 specs, 152 tests), and full static fixture/helper/setup/config review (GOV-E2E-003, 25/25 files) are complete. Preserve E2E-001–E2E-009. Targeted lifecycle/hook/reconnect/persistence/test-hook trace is complete (GOV-E2E-004); scripts static audit is complete (14/14; BUILD-001–BUILD-007 OPEN); `.github/` inventory and trust-boundary design review are complete (GOV-GHCI-001/002); BUILD-008/009 remediation is implemented on a separate unmerged branch and remains OPEN pending verification; BUILD-010–BUILD-014 remain OPEN. Do not restart without changed source or a named verification gap.
6. System dependency map: npm lockfile transitive resolution remains complete from GOV-DEPMAP-002. Static source import inventory now covers 334 code modules across core/server/webview/adapters/scripts, and terminal/process ownership is mapped in GOV-DEPMAP-003/004. Remaining: bundle/metafile and package-output comparison, symbol-level call/lifecycle details not established statically, and authorized runtime verification. Do not restart completed module reviews.
7. Complete infrastructure verification: compare the mapped source imports and lockfile graph with esbuild/Vite metafiles, SBOM/license evidence and npm/VSIX outputs; then run the authorized build/test matrix. Scoped `scripts/` and `.github/` reviews are complete and must not be repeated.
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

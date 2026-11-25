# E2E Module Audit Record

**Status:** IN PROGRESS — inventory/SHA reconciliation complete (47/47); assertion-level review complete (21/21 spec files, 152/152 tests); E2E-003–E2E-006 are OPEN test-quality findings; full static review complete for 25/25 fixture/helper/setup/config files (GOV-E2E-003); targeted lifecycle/hook/reconnect/persistence/test-hook cross-module static trace complete (GOV-E2E-004); scripts static audit is complete (14/14; BUILD-001–BUILD-007 recorded); `.github/` CI/release workflow static audit is complete (10/10; BUILD-008–BUILD-014 recorded).  
**Source baseline:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated=false`).  
**Historical documentation snapshot:** `docs/hybrid-llm-engine-direction@cbf2d7ed7f711bc44fb619e43d86c560e1cf7d7e` (pre-publication provenance; not current HEAD).  
**Inventory:** 47/47 blobs; 7 directories; 0 missing, 0 extra, 0 SHA mismatches against the documentation branch.  
**Session:** GOV-E2E-001.  
**Detailed owner:** [E2E DEEP-AUDIT](../../e2e/DEEP-AUDIT.md).  
**File identity / coverage owner:** [E2E FILE-REFERENCE](../../e2e/FILE-REFERENCE.md).

## Document ownership

| Concern | Owner |
|---|---|
| Consolidated repository status | [Audit register](../AUDIT-REGISTER.md) |
| Session history and SHA snapshots | [Evidence log](../EVIDENCE-LOG.md) |
| E2E technical findings, test design and cross-module gaps | [DEEP-AUDIT.md](../../e2e/DEEP-AUDIT.md) |
| Per-file path, mode, size, blob SHA and review state | [FILE-REFERENCE.md](../../e2e/FILE-REFERENCE.md) |
| E2E architecture navigation and suite coverage statement | [README.md](../../e2e/README.md) |

## Current coverage

- 47/47 tracked blobs are present in the complete recursive source tree and were fetched individually.
- 47/47 path/blob-SHA pairs match the documentation-branch snapshot.
- 21 spec files contain 152 test declarations; all 152 have `@area:` tags, matching the generated inventory in the source E2E README.
- Eight platform skip guards were inventoried separately from the 152 declarations.
- Assertion-level review is complete for all 21 spec files (TEST-INVENTORIED); no tests were executed.
- Full-file static source review is complete for all 25 non-spec harness files: 6 fixtures/shims, 15 helpers, global setup and 3 configuration files (SOURCE-VERIFIED; GOV-E2E-003).
- Four assertion-quality gaps E2E-003–E2E-006 are recorded in the deep audit.
- No tests, builds, lint, typecheck or runtime experiments were executed.

## Findings

Preserve E2E-001 through E2E-009 in [DEEP-AUDIT.md](../../e2e/DEEP-AUDIT.md):
- **E2E-001:** standalone process termination fallback does not reliably escalate after SIGTERM; static, OPEN.
- **E2E-002:** standalone port-0 allocation closes the socket before the CLI binds; static race, OPEN.
- **E2E-003:** seeded-layout Areas gate test is confounded by the host Directory gate; assertion gap, OPEN.
- **E2E-004:** carpet/furniture coexistence test does not assert same-coordinate furniture; assertion gap, OPEN.
- **E2E-005:** mobile modal fit test omits vertical bounds; assertion gap, OPEN.
- **E2E-006:** pet persistence test verifies count, not restored identities/types; assertion gap, OPEN.
- **E2E-007:** `PIXEL_AGENTS_E2E_RUN_ID=..` escapes the intended artifact namespace and broadens recursive Allure cleanup; static, OPEN.
- **E2E-008:** stale VS Code download lock has no owner/PID recovery and can block setup for ten minutes; static, OPEN.
- **E2E-009:** mock scenario queue uses an unlocked cross-process read-modify-write claim; concurrent mock Claude processes can duplicate/lose scenarios; static, OPEN.

Do not duplicate these detailed records in the register. Cross-link canonical findings in the owning modules: server F-01/F-03/F-04/F-05/F-09; adapters A-03/A-05/A-06; webview WV-RESYNC-01/02, WV-APP-01, WV-TR-01 and WV-PROTO-01/02/03; server F-08 / webview WV-C08. GOV-E2E-004 records the lifecycle, hook, reconnect, persistence and test-hook boundary trace without duplicating those findings.

## Next action

GOV-E2E-004 completed the targeted lifecycle/hook/reconnect/persistence/test-hook static trace. Do not repeat it without changed source or a named evidence gap. Next E2E work is remediation planning for E2E-001–E2E-009 and, only when explicitly authorized, execution of selected tests and runtime verification. Scripts (14/14) and `.github/` (10/10) static audits are complete; preserve BUILD-001–BUILD-014 in their owning records.

# Scripts Module Audit Record

**Status:** STATIC AUDIT COMPLETE — 14/14 tracked blobs individually fetched, SHA-reconciled and reviewed. Seven findings BUILD-001–BUILD-007 are OPEN.  
**Source baseline:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated=false`).  
**Documentation snapshot before publication:** `docs/hybrid-llm-engine-direction@ed01e33e6be1914509518d6fe56a8fe5f5b2944d`.  
**Detailed owner:** [Scripts deep audit](../../SCRIPTS-AUDIT.md).  
**File identity owner:** [Scripts file reference](../../SCRIPTS-FILE-REFERENCE.md).

## Document ownership
| Concern | Owner |
|---|---|
| Consolidated repository status | [Audit register](../AUDIT-REGISTER.md) |
| Session history | [Evidence log](../EVIDENCE-LOG.md) |
| Scripts technical findings | [SCRIPTS-AUDIT.md](../../SCRIPTS-AUDIT.md) |
| Per-file identity and review state | [SCRIPTS-FILE-REFERENCE.md](../../SCRIPTS-FILE-REFERENCE.md) |

## Coverage
- Complete recursive source tree: 14 blobs directly under `scripts/`; all individually fetched.
- Source/docs branch comparison: 14/14 path/blob-SHA pairs match; 0 added, 0 removed, 0 changed.
- Static source review: 14/14 files complete, including the three HTML utilities.
- `npm-package-contract.test.mjs`: 7 declarations; TEST-INVENTORIED only.
- No tests, builds, lint, typecheck, generation, package, publish or runtime operations executed.

## Findings
Preserve BUILD-001 through BUILD-007 in the deep audit:
- BUILD-001: JSONL viewer event labels render untrusted tool names as HTML; OPEN.
- BUILD-002: Asset Manager renders a free-form group role into innerHTML without escaping; OPEN.
- BUILD-003: imported group hierarchy is not checked for cycles before recursive traversal; OPEN.
- BUILD-004: discarded assets inside groups are still included in export; OPEN.
- BUILD-005: package.json import-tileset command references a missing script file; OPEN.
- BUILD-006: Allure failure path bypasses staged-results cleanup; OPEN.
- BUILD-007: npm package smoke child is not awaited after SIGKILL; OPEN.

Existing E2E-001/E2E-002 are cross-referenced for related process-stop and port-allocation patterns; no duplicate IDs were created.

## Cross-scope continuation
The `.github/` and CI/release audit is now recorded separately in [GitHub CI/release module record](github-ci.md), with BUILD-008–BUILD-013. The latest GOV-GHCI-002 continuation expands the evidence for BUILD-009 to include `npm pack`/`prepack` execution while `id-token:write` is available; it does not create a new finding ID. This scripts record owns BUILD-001–BUILD-007; preserve all IDs and existing E2E/server/adapters/webview finding IDs. Runtime verification remains unauthorized.

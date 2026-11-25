# E2E Module Audit Record

**Status:** IN PROGRESS — inventory and SHA reconciliation complete; 47/47 blobs fetched; 21/21 spec files structurally inventoried; assertion-level and cross-module review remains open.  
**Source baseline:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` (`truncated=false`).  
**Latest documentation snapshot before publication:** `docs/hybrid-llm-engine-direction@cbf2d7ed7f711bc44fb619e43d86c560e1cf7d7e`.  
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
- Test assertions are structurally inventoried; assertion-level review is still IN PROGRESS.
- No tests, builds, lint, typecheck or runtime experiments were executed.

## Findings

Preserve E2E-001 and E2E-002 in [DEEP-AUDIT.md](../../e2e/DEEP-AUDIT.md):
- **E2E-001:** standalone process termination fallback does not reliably escalate after SIGTERM; static, OPEN.
- **E2E-002:** standalone port-0 allocation closes the socket before the CLI binds; static race, OPEN.

Do not duplicate these detailed records in the register. Cross-link canonical findings in the owning modules: server F-01/F-03/F-05; adapters A-03/A-05/A-06; webview WV-RESYNC-01/02, WV-APP-01, WV-TR-01 and WV-PROTO-01/02/03; server F-08 / webview WV-C08.

## Next action

Continue assertion-level review of all 21 spec files and full review of remaining harness files, then perform targeted source-contract traces. Inspect the E2E runner/inventory/report scripts and CI workflows under their owning `scripts/` and `.github/` scopes. Keep execution/runtime verification OPEN until explicitly authorized.

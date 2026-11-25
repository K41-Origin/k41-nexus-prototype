# End-to-End Tests — Current Architecture

**Status:** IN PROGRESS — complete tracked-file inventory/SHA reconciliation (47/47); assertion-level review complete (21/21 specs, 152 tests); harness source review continues.  
**Source baseline:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Inventory:** 47/47 tracked blobs under `e2e/`; 21 spec files / 152 test declarations. No tests have been executed in this audit. Four assertion-quality gaps (E2E-003–E2E-006) are recorded in DEEP-AUDIT.md.

## Audit documents

- [E2E DEEP-AUDIT](DEEP-AUDIT.md) — harness architecture, findings, cross-module links, limitations and continuation.
- [E2E FILE-REFERENCE](FILE-REFERENCE.md) — complete path/mode/size/blob-SHA ledger and per-file review state.
- [Repository E2E governance record](../governance/modules/e2e.md) — consolidated module status and document ownership.

## Scope

The E2E module includes Playwright configuration, global setup, VS Code and standalone fixtures, mock Claude process shims, scenario builders, hook helpers, office/editor/webview helpers, narration, and all VS Code/standalone test specs.

The source suite README at `e2e/README.md` remains the product-facing source of truth for automated test coverage, declared gaps and manual pre-release smoke checks. This architecture document records audit status and links to the technical audit; it does not duplicate the suite's test inventory.

## Verification boundary

A file being present in the tree or fetched is not equivalent to a completed source review. A test being inventoried is not equivalent to being executed. Runtime, browser, VS Code host, PTY and CI evidence remain OPEN until actually obtained and recorded.

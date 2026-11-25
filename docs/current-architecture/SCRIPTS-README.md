# Scripts and Tooling — Current Architecture

**Status:** STATIC AUDIT COMPLETE — 14/14 tracked blobs individually fetched, SHA-reconciled and reviewed at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. Seven BUILD findings are OPEN. No scripts were executed.

## Audit documents
- [Scripts deep audit](SCRIPTS-AUDIT.md) — responsibilities, source flows, findings and verification boundaries.
- [Scripts file reference](SCRIPTS-FILE-REFERENCE.md) — complete inventory with mode, bytes, lines, blob SHA and review status.
- [Governance module record](governance/modules/scripts.md) — consolidated scope and next action.

## Scope
All 14 tracked blobs directly under `scripts/`: generation/validation, E2E runner/inventory, Allure/Vercel reporting, npm/VSIX packaging verification/publication, and three standalone HTML utilities. Package manifest script declarations were inspected as integration context.

## Verification boundary
Static source review only. The seven tests in `npm-package-contract.test.mjs` were inventoried, not executed. No generation, test, build, lint, typecheck, package, publish, browser, VS Code host or runtime operation was run. No source, script, package manifest or dependency was modified during this audit.

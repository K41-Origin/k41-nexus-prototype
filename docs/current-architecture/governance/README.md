# Repository Audit Governance

**Status:** ACTIVE — governance baseline established; module records are populated as audits are verified.  
**Initial module recorded:** `core/`  
**Canonical governance branch:** `main`; latest verification at `b1104a808cf70382c2bd14a94d372d907a500cda`.  
**Documentation HEAD at the start of GOV-DOCGOV-001:** `02b3118edf38713cc55b581a916fd61dea8bfd1b`  
**Current source verification snapshot:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Webview source verification snapshot:** `8fc045de9ddae905cc131498d918ddbd64cc8bca`

This directory is the **single coordination layer** for repository audits. It does not replace technical module documentation. It controls how evidence, coverage, findings, dependencies, and unfinished work are recorded and connected.

## Source-of-truth hierarchy

1. **Current source tree at the explicitly recorded commit** — authority for what code exists.
2. **Module audit record** — authority for reviewed scope, verified behavior, findings, limitations, and remaining work.
3. **File reference / ledger** — authority for per-file identity (path + blob SHA), review status, and coverage.
4. **Evidence log** — append-only record of audit sessions, snapshots, verification actions, and resulting changes.
5. **Audit register** — navigation and consolidated status across all repository areas.
6. **Architecture/module documents** — explanatory context; they must link back to evidence and must not silently override source.

When documents disagree, do not choose whichever sounds newer. Re-fetch the source at the active audit commit, establish the discrepancy, then update the owning record and log the correction.

## Integrated documents

| Document | Responsibility |
|---|---|
| [Audit register](AUDIT-REGISTER.md) | Consolidated status and entry point for every module |
| [Audit method](AUDIT-METHOD.md) | Mandatory workflow, evidence levels, SHA/no-duplication rules, finding lifecycle |
| [Evidence log](EVIDENCE-LOG.md) | Dated/session-based record of what was actually fetched, compared, tested, or not tested |
| [Core audit record](modules/core.md) | Verified core source/docs audit, baseline, findings and open cross-layer questions |
| [Server audit record](modules/server.md) | Verified server inventory/source reconciliation, findings and runtime/test limitations |
| [Webview UI audit record](modules/webview-ui.md) | Webview inventory reconciliation, historical coverage, findings and remaining audit gaps |
| [Adapters audit record](modules/adapters.md) | VS Code adapter static audit coverage, A-01–A-10, source baseline and remaining integration gaps |
| [E2E audit record](modules/e2e.md) | E2E inventory, assertion-review status, E2E-001–E2E-009 and cross-module coverage gaps |
| [Scripts audit record](modules/scripts.md) | Complete 14/14 scripts inventory, source review, BUILD-001–BUILD-007 and tooling gaps |
| [GitHub CI/release audit record](modules/github-ci.md) | Complete 10/10 `.github/` inventory, workflow permissions/triggers, secret boundaries, artifact provenance and BUILD-008–BUILD-014 |
| [System dependency map](../SYSTEM-DEPENDENCY-MAP.md) | Single owner for evidence-backed cross-module import, runtime ownership, lifecycle, protocol and trust-boundary edges; static map IN PROGRESS |
| [Infrastructure module record](modules/infrastructure.md) | Integrated build/package/dependency/test/CI audit; PARTIAL |
| [System dependency map module record](modules/system-dependency-map.md) | Coverage/status pointer for the cross-module map |
| [Architecture/ADR module record](modules/architecture-adr.md) | Reconciliation of current implementation, three ADRs and future hybrid-engine direction |
| [Architecture/ADR reconciliation](../../ARCHITECTURE-ADR-RECONCILIATION.md) | Technical comparison; 12 future-engine design decisions remain OPEN |
| [Infrastructure audit documents](../infrastructure/README.md) | Integrated build/package/test/CI static audit; PARTIAL, with transitive/bundle/runtime verification OPEN |

## Existing technical records retained

- [Architecture index](../README.md)
- [Core module overview](../core/MODULE-OVERVIEW.md)
- [Core file reference](../core/FILE-REFERENCE.md)
- [Core protocol](../core/PROTOCOL.md)
- [Core dependencies](../core/DEPENDENCIES.md)
- [Webview deep audit](../webview-ui/DEEP-AUDIT.md), [webview file ledger](../webview-ui/FILE-REFERENCE.md), and its protocol/asset/state/rendering/transport documents
- [Server deep audit](../server/DEEP-AUDIT.md) and [server file reference](../server/FILE-REFERENCE.md)
- [Adapters deep audit](../adapters/DEEP-AUDIT.md) and [adapter file reference](../adapters/FILE-REFERENCE.md)
- [E2E deep audit](../e2e/DEEP-AUDIT.md) and [E2E file reference](../e2e/FILE-REFERENCE.md)
- [Scripts deep audit](../SCRIPTS-AUDIT.md), [scripts file reference](../SCRIPTS-FILE-REFERENCE.md) and [scripts overview](../SCRIPTS-README.md)
- [GitHub CI/release deep audit](../github-ci/DEEP-AUDIT.md), [file reference](../github-ci/FILE-REFERENCE.md) and [overview](../github-ci/README.md)

These documents are linked, not copied wholesale. The governance register points to the owning record for each detail. Legacy scripts navigation stubs under `scripts/` are aliases only; the canonical scripts records are `SCRIPTS-README.md`, `SCRIPTS-AUDIT.md` and `SCRIPTS-FILE-REFERENCE.md`.

## Rules

- No audit status without a commit SHA and a defined scope.
- No file is considered reviewed based on its filename, a search snippet, or a previous assistant summary alone.
- Never repeat an unchanged, adequately reviewed file for the same question. Revisit it only for a recorded gap, a new cross-reference, a changed source SHA, or a changed audit question.
- A changed SHA invalidates the old file-level review for the changed content; it does not invalidate unrelated module work.
- A finding must have a stable ID, evidence location, impact, confidence, and verification state.
- Static inspection, test inventory, executed tests, build results, and runtime reproduction are separate evidence categories.
- Do not modify application source, generated protocol files, configuration, or dependencies during an audit unless explicitly authorized.
- Keep findings open until evidence closes them. Do not mark a finding fixed because a proposed fix exists.
- Every audit session ends with an evidence-log entry and an updated register/module record if status changed.

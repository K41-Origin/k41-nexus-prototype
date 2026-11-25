# Repository Audit Governance

**Status:** ACTIVE — governance baseline established; module records are populated as audits are verified.  
**Initial module recorded:** `core/`  
**Branch:** `docs/hybrid-llm-engine-direction`  
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

## Existing technical records retained

- [Architecture index](../README.md)
- [Core module overview](../core/MODULE-OVERVIEW.md)
- [Core file reference](../core/FILE-REFERENCE.md)
- [Core protocol](../core/PROTOCOL.md)
- [Core dependencies](../core/DEPENDENCIES.md)
- [Webview deep audit](../webview-ui/DEEP-AUDIT.md), [webview file ledger](../webview-ui/FILE-REFERENCE.md), and its protocol/asset/state/rendering/transport documents
- [Server deep audit](../server/DEEP-AUDIT.md) and [server file reference](../server/FILE-REFERENCE.md)
- [Adapters deep audit](../adapters/DEEP-AUDIT.md) and [adapter file reference](../adapters/FILE-REFERENCE.md)

These documents are linked, not copied wholesale. The governance register points to the owning record for each detail.

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

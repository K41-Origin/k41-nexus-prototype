# Current Architecture Audit

**Status:** Evidence-backed architecture audit in progress  
**Baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`

## Governance

The repository audit is coordinated through [Audit Governance](governance/README.md). Start there before any module audit. The [Audit Register](governance/AUDIT-REGISTER.md) is the consolidated status index; the [Audit Method](governance/AUDIT-METHOD.md) defines SHA-first no-duplication and evidence rules; the [Evidence Log](governance/EVIDENCE-LOG.md) records actual work performed.

## Module audit documents

| Area | Scope |
|---|---|
| [Preliminary audit](00-PRELIMINARY-AUDIT.md) | Initial evidence-backed findings and remaining audit checklist |
| [Project overview](00-PROJECT-OVERVIEW.md) | Product and repository context |
| [Core](core/README.md) | Shared contracts, protocol, domain utilities and asset pipeline — static audit recorded |
| [Server](server/DEEP-AUDIT.md) | Server runtime audit findings and verification limitations |
| [Webview UI](webview-ui/DEEP-AUDIT.md) | Deep static audit progress and findings |
| [Adapters](adapters/README.md) | Host-specific integration audit |
| [System dependency map](SYSTEM-DEPENDENCY-MAP.md) | Cross-module dependency and lifecycle evidence |

## Audit rules

1. Audit current source at a pinned commit SHA.
2. Compare file blob SHAs before rereading; preserve unchanged completed work.
3. Inspect changed files and documented gaps only.
4. Record exact paths, blob SHAs, line ranges, and evidence levels.
5. Keep static findings separate from runtime-confirmed behavior.
6. Keep architecture direction separate from verified current-state facts.
7. Do not change application source during an audit unless explicitly authorized.

See [governance/AUDIT-METHOD.md](governance/AUDIT-METHOD.md) for the complete operating procedure.

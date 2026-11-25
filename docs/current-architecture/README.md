# Current Architecture Audit

**Status:** Evidence-backed architecture audit in progress  
**Historical module baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`
**Latest cross-layer static source snapshot:** `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58` (based on `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`); system dependency, infrastructure and ADR reconciliation are documented against this snapshot.

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
| [System dependency map](SYSTEM-DEPENDENCY-MAP.md) | Cross-module dependency, runtime ownership, protocol and trust-boundary evidence — static map in progress |
| [Infrastructure](infrastructure/README.md) | Integrated build, dependencies, tests and CI/CD audit |
| [Architecture/ADR reconciliation](ARCHITECTURE-ADR-RECONCILIATION.md) | Current implementation vs future Hybrid LLM Engine direction and existing ADRs |

## Audit rules

1. Audit current source at a pinned commit SHA.
2. Compare file blob SHAs before rereading; preserve unchanged completed work.
3. Inspect changed files and documented gaps only.
4. Record exact paths, blob SHAs, line ranges, and evidence levels.
5. Keep static findings separate from runtime-confirmed behavior.
6. Keep architecture direction separate from verified current-state facts.
7. Do not change application source during an audit unless explicitly authorized.

See [governance/AUDIT-METHOD.md](governance/AUDIT-METHOD.md) for the complete operating procedure.

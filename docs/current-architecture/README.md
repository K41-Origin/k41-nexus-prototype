# Current Architecture Documentation

**Status:** Documentation structure established; detailed audit in progress  
**Baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`

This directory documents the repository as it exists today. It is the evidence-based source for understanding current modules, file responsibilities, dependencies, data flow, lifecycle, and constraints.

It must remain separate from `docs/refactoring/`, which describes intended changes and future architecture.

## Documentation map

| Area | Scope |
|---|---|
| [Project overview](00-PROJECT-OVERVIEW.md) | Product identity, runtime modes, repository map |
| [Core](core/README.md) | Shared contracts, protocol, utilities |
| [Server](server/README.md) | Runtime, providers, persistence, HTTP/WS, terminals |
| [Adapters](adapters/README.md) | Host/editor integrations |
| [Webview UI](webview-ui/README.md) | React UI, rendering, state, client transport |
| [Scripts](scripts/README.md) | Build, generation, packaging and maintenance scripts |
| [E2E](e2e/README.md) | End-to-end test architecture |
| [Infrastructure](infrastructure/README.md) | Build, test, CI and dependency infrastructure |
| [System dependency map](SYSTEM-DEPENDENCY-MAP.md) | Cross-module dependency and data-flow map |
| [Preliminary audit](00-PRELIMINARY-AUDIT.md) | Initial evidence-backed findings and remaining audit checklist |

## Audit rules

1. Document actual code, not intended future design.
2. Every source file must be accounted for in its module's `FILE-REFERENCE.md`.
3. Mark uninspected files `NOT AUDITED`; never infer detailed behavior from filenames alone.
4. Record imports separately from runtime calls/data flow where evidence permits.
5. Identify consumers, side effects, lifecycle ownership, error behavior, and tests.
6. Link to source paths and relevant tests.
7. Separate verified facts, inferences, proposals, and open questions.
8. Update the relevant documentation when source behavior changes.

## File reference record

Each file entry should cover:

- **Status:** VERIFIED / PARTIALLY AUDITED / NOT AUDITED
- **Responsibility:** primary purpose and boundaries
- **Components:** exported and important internal functions, classes, types
- **Imports/dependencies:** direct dependencies
- **Consumers:** known callers/importers
- **Inputs and outputs:** data/contracts
- **Data flow and lifecycle:** when and how it participates
- **Side effects:** filesystem, process, network, timers, state mutation
- **Error handling and constraints:** important invariants
- **Tests:** relevant test files and uncovered behavior
- **Refactoring notes:** observations, not decisions

## Completion policy

A directory is not considered audited merely because its README exists. Mark it complete only after all handwritten source files and relevant tests/configuration have been inspected and its dependency/lifecycle claims have evidence.

# Architecture Direction and ADR Implementation Reconciliation

**Status:** PARTIAL — three existing ADRs reconciled statically; current implementation boundaries compared with the hybrid-engine direction. The future architecture is not an implementation specification and 12 design decisions remain OPEN.

**Documentation branch:** `docs/hybrid-llm-engine-direction`  
**Source branch:** `security/remediate-build-008-009`  
**Source commit:** `86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Source base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`

## 1. Reconciliation rule

The architecture direction dated 2026-11-24 is a directional design note, explicitly not a frozen implementation specification. Its future-state statements must not be reported as shipped features. The three ADRs are decisions about the existing Pixel Agents implementation and are reconciled separately below.

## 2. Hybrid LLM Engine direction vs current implementation

| Directional concept | Current repository evidence | Reconciliation |
|---|---|---|
| One Main Agent coordinating specialized LLM workers | Current `AgentRuntime` manages sessions/events/teammates for the existing CLI provider. `core/src/provider.ts` defines hook and team capability contracts, not a generic LLM worker/scheduler contract. Server provider registry is `[claudeProvider]`. | NOT IMPLEMENTED as a generic Main Agent/worker orchestration architecture. Existing CLI subagents/teams are provider-originated capabilities, not evidence of a first-party worker scheduler. |
| Task-based workers with explicit budgets/retries/permissions | Existing task/subagent event tracking and terminal/process lifecycle are tied to provider events. No generic task queue, worker execution contract, model-call budget engine or recursive delegation policy was identified in the reviewed contracts. | OPEN / FUTURE DESIGN. Do not equate CLI subagent lifecycle with the proposed worker lifecycle. |
| C as leading core-engine candidate | Complete source tree has no `.c`, `.h`, `CMakeLists.txt` or `.cmake` files. Current implementation is TypeScript/Node, React/Vite and VS Code integration. | C is a candidate only; no C engine or benchmark-backed language decision is implemented. |
| Single-process modular engine first | Existing server/runtime is composed in-process for both embedded and standalone hosts. Development-only PTY-host mode can split terminal processes. | PARTIAL architectural precedent, not implementation of the proposed C engine or its module boundaries. |
| Model Gateway/provider topology | Current bundled hook-provider registry contains Claude only; server dependency inventory identifies no outbound LLM provider SDK/API client. | NOT IMPLEMENTED as a generic model gateway or per-task provider selector. |
| Context/token management | Existing context-usage reporting and usage bridge expose provider/account usage; no evidence establishes the proposed worker token-budget manager or context-isolation policy. | PARTIAL adjacent capability; proposed token budgeting remains OPEN. |
| Host independent from UI | Existing standalone CLI serves a browser SPA over HTTP/WebSocket; VS Code embeds the server and uses postMessage. | PARTIAL current host/client separation. |
| Laptop + Android remote clients | No Android/Gradle/Manifest project exists in the complete source tree. Webview mobile E2E is a browser viewport suite, not a native Android client. | Android client is NOT IMPLEMENTED. |
| Remote security: TLS, device registration, scopes, audit | Standalone server has token/Host/origin privilege checks; non-loopback HTTP is not itself a TLS/device identity system. | PARTIAL local trust controls; proposed remote access security remains OPEN. |
| Memory/concurrency/event/task/persistence contracts | Existing agent/session state, file persistence, timers, hooks and WebSocket event flow are product-specific. They do not settle the new engine's ownership/queue/backpressure/recovery semantics. | OPEN design decisions. |
| Performance targets and measurement | No benchmark harness or agreed idle/active RAM, CPU, startup, latency, throughput, task-count or token-cost targets were identified in the reviewed tree. | NOT MEASURED; no optimization claim supported. |
| Preserve/reuse existing capabilities | Existing code has VS Code host integration, Claude hooks, terminal/PTy, file-backed state, HTTP/WebSocket, AsyncAPI control-plane protocol and React/Canvas UI. | IMPLEMENTED current capabilities; migration boundaries still need design before any replacement. |

### Current architecture in one sentence

The repository is a TypeScript/Node Pixel Agents product with a shared core contract/domain layer, a server/runtime layer, a VS Code host adapter, a React webview/browser client, Claude hook integration, terminal/process handling, persistence and a separate control-plane protocol. It is not yet the proposed C-based Hybrid LLM Agent Engine.

## 3. Existing ADR reconciliation

### ADR-0001 — Consent choices send immediately; revisions are absolute state commands

**Disposition: SOURCE-VERIFIED ALIGNMENT (static).**

Evidence:
- Wire schema defines `hooksConsentResponse` and exact choices `install | notNow | never`: `core/asyncapi.yaml:1404-1442`, blob `ff1247118819fe68852da11996c660df1726aa66`.
- Gate checks installed state, durable consent, hooks preference and privileged client before asking: `server/src/providers/hook/consentGate.ts:11-43`, blob `858a83230bdeac95d3858a3b4c1134de96ae83e8`.
- Action mapping explicitly models tri-state consent and absolute revision behavior: `consentGate.ts:46-80`, same blob.
- Executor serializes answers through one process-wide promise queue, re-reads install state when an answer reaches the head, and applies install/disable/revert/revertDecline behavior: `server/src/providers/hook/consentExecutor.ts:49-140`, blob `523dbde800d275c8f070a7d44ac48956e4034b63`.
- Client sends the choice immediately: `webview-ui/src/hooks/useIntroTour.ts:69-82`, blob `4fc18a2eef5bb2b73bcf6a4bf5facfc17d25a28d`; reducer preserves the answered tour snapshot and uses provider-specific status arrival: `webview-ui/src/hooks/introTourState.ts:18-80`, blob `f64a1f9c2c598f812360413f958783fe1d90c2c5`.
- Tests exist in `server/__tests__/consentGate.test.ts`, `server/__tests__/consentFlow.test.ts`, `server/__tests__/consentCopy.test.ts` and E2E consent spec. They are inventoried evidence only here; not executed.

No static contradiction with ADR-0001 was found in the inspected implementation path. Cross-window/multi-process concurrency remains a separately documented limitation; a process-global queue does not serialize two independent processes.

### ADR-0002 — Wire contract follows the glossary

**Disposition: SOURCE-VERIFIED ALIGNMENT (static), with legacy persistence vocabulary retained for migration.**

Evidence:
- `CONTEXT.md:61-63` defines Directory and explicitly says to avoid “folder”, “workspace folder” and “project” for the concept; blob `19d6635e024f007cc54c35e5a9aeebcfe4cf77ca`.
- AsyncAPI defines `LaunchAgent.directoryPath`, Directory and `directoryNames` vocabulary. `core/asyncapi.yaml` blob `ff1247118819fe68852da11996c660df1726aa66`.
- Generated `core/src/messages.ts` contains the directory vocabulary and no `workspaceFolders`, `folderPath` or `folderNames` wire names; blob `2fd177f12327c03ae5bdbc4beb83360121c5f703`.
- The checked AsyncAPI/generated message sources had zero occurrences of those three legacy wire identifiers in this snapshot.

Nuance: `server/src/agentPersistence.ts` still reads legacy persisted `folderName` as a fallback for `directoryName`. This is on-disk migration compatibility, not a return of the old wire vocabulary; the migration should not be removed without a compatibility decision.

### ADR-0003 — A look is a palette and a repeat

**Disposition: PARTIAL SOURCE-VERIFIED ALIGNMENT.**

Evidence:
- AsyncAPI uses `repeat` in agent and seat contracts; `SeatAssignment` requires `palette`, `repeat` and `seatId`: `core/asyncapi.yaml:1757-1770`, blob `ff1247118819fe68852da11996c660df1726aa66`.
- `agentCreatedMessage` emits `palette` and `repeat`: `server/src/agentMessages.ts:8-22`, blob `df521f7a6d9ddbad8d7ee0c567df4054c500b9ea`.
- Persistence writes `repeat`; restore maps old `clothesHue`/`hueShift` to repeat (zero remains zero, non-zero becomes one): `server/src/agentPersistence.ts:23-41,51-86`, blob `0dcced81be9762d924ebed02aeea3c6c24ece0bf`.
- FileStateAdapter stores seat `palette/repeat/seatId`: `server/src/fileStateAdapter.ts:84-102`, blob `62051790826ff7287d21dca93f42e5a86dc41908`.
- Webview repeat palette transformation is implemented in `webview-ui/src/characters/repeatClothes.ts:1-58`, blob `c3d6825b008b1e1a4e25a1b6980be7727c4538ff`.

The core repeat contract, persistence write/read migration and clothes-color transform align with the ADR. The full `characterSpritesLoaded` producer/consumer behavior described by the ADR was not exhaustively traced in this reconciliation; retain PARTIAL until that specific asset-message path is mapped. No test or migration fixture was executed.

## 4. The 12 unresolved engine decisions

The directional note's twelve questions remain OPEN; no new decision is inferred from the existing Pixel Agents implementation:

1. Worker execution lifecycle.
2. Model/provider topology.
3. Main Agent runtime and control loop.
4. Concurrency model.
5. Memory ownership/allocation strategy.
6. Event queue semantics, ordering and backpressure.
7. Task state, cancellation, deadlines, retries and recovery.
8. Persistence across restart.
9. Tool isolation and process boundaries.
10. Remote networking, authentication and authorization.
11. Laptop/Android client responsibilities and UI scope.
12. Quantified performance and LLM-cost targets.

## 5. Recommended decision sequence

1. Define product-level requirements and threat model before choosing C libraries or concurrency primitives.
2. Specify task lifecycle, worker contract, cancellation and bounded execution before scheduler implementation.
3. Specify state ownership, persistence, event delivery/backpressure and failure recovery as one coherent lifecycle model.
4. Define Model Gateway/provider interfaces and token/context accounting; explicitly distinguish LLM calls from deterministic tools.
5. Define host-client protocol and remote trust model before Android or remote-control implementation.
6. Set measurable resource/latency/throughput/token-cost targets, then compare C/Rust/TypeScript prototypes with repeatable workloads.
7. Map existing Pixel Agents capabilities to reuse/adapt/replace decisions before any migration.

These are recommendations for decision order, not accepted architecture decisions.

## 6. Limitations and evidence boundary

- Static source/document reconciliation only.
- No complete repository-wide call graph, build output, dependency graph or runtime behavior was generated.
- No tests, build, typecheck, lint, benchmark, model API call, network experiment or Android build was run.
- Existing source findings CORE-PKG-01, CORE-ARCH-01, CORE-CONS-01, CORE-TEST-01, F-01–F-09, A-01–A-10, WV-*, E2E-* and BUILD-* retain their owning records and IDs. This document creates no duplicate finding.
- “Not implemented” means no implementation was identified in the complete source tree or reviewed contracts; it is not a statement about untracked external prototypes.

See [governance/modules/architecture-adr.md](governance/modules/architecture-adr.md) and [governance/AUDIT-REGISTER.md](governance/AUDIT-REGISTER.md) for status ownership.

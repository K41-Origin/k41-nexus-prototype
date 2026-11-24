# Current Architecture Audit

**Status:** Preliminary audit — evidence-backed, not yet exhaustive  
**Date:** 2026-11-24  
**Baseline:** `main` at `4dd14d612fd37d89ffbbc977959f572395c4eec8`  
**Working branch:** `docs/hybrid-llm-engine-direction`  
**Scope:** Current Pixel Agents repository architecture, before any refactoring implementation

> This document records the architecture that exists in the repository today. It is not a target-architecture specification. Findings marked **Verified** are supported by inspected repository files. Areas marked **Pending inspection** must not be treated as fully audited. No implementation or performance conclusions are implied by this document.

---

## 1. Executive summary

The current repository is a mature TypeScript/Node.js application named **Pixel Agents**, not an existing general-purpose LLM orchestration engine. Its current product purpose is to represent AI coding sessions as animated characters in a pixel-art office and provide interfaces to launch, observe, and interact with those sessions.

The codebase already has meaningful architectural boundaries:

- a shared protocol and interface layer in `core/`;
- a lifecycle/runtime and HTTP/WebSocket host in `server/`;
- a React browser/webview client in `webview-ui/`;
- a VS Code integration in `adapters/vscode/`;
- a standalone CLI and supporting scripts;
- extensive unit, integration, and end-to-end tests.

The repository's documented dependency direction is deliberately layered. This is a valuable existing constraint and should be preserved or consciously replaced through an explicit decision—not broken accidentally during refactoring.

The proposed K41 Hybrid LLM Agent Engine is a substantial product and architecture evolution. It should not be described as a simple internal rewrite of the current server. The current application is primarily an agent-session observer/controller and UI host, with Claude Code as the shipped provider. A future Main Agent + task-worker orchestration system introduces new responsibilities: planning, task decomposition, scheduling, model/provider calls, context construction, token/cost accounting, tool authorization/execution, worker lifecycle, and durable task state. These capabilities are not established as a complete orchestration engine in the inspected current architecture.

**Initial assessment:** treat the existing codebase as a source of reusable behavior, contracts, UI, integration knowledge, and tests. Do not assume its current server/runtime boundaries map one-to-one onto the future engine.

## 2. Repository identity and runtime baseline

### 2.1 Product and package identity

**Verified from `package.json` and `README.md`:**

- Package name: `pixel-agents`.
- Display name: `Pixel Agents`.
- Current package version in the inspected baseline: `1.6.0`.
- Package description: pixel-art office for Claude Code agents.
- Published forms: VS Code extension and standalone npm CLI.
- Node.js engine requirement: `>=20`.
- VS Code engine requirement: `^1.105.0`.
- npm workspaces: `server` and `webview-ui`.
- Repository metadata points to the upstream Pixel Agents project.

The README describes Claude Code as the currently supported CLI; other providers are roadmap items. The repository therefore contains provider abstractions, but the existence of an abstraction must not be confused with production support for multiple providers.

### 2.2 Main user-facing execution modes

1. **VS Code extension:** agents are launched in VS Code terminals; the office is rendered in a VS Code webview.
2. **Standalone CLI:** `npx pixel-agents` starts a local server and serves the office as a browser application; standalone mode can manage embedded terminals.
3. **Remote browser/mobile access:** the README documents browser/phone access to the standalone host, including a Tailscale-oriented workflow.

These are two principal adapters/surfaces over shared code, not two entirely separate products.

## 3. High-level source layout

The recursive Git tree for the audited `main` commit reports **739 tracked blobs** and `truncated: false`. The repository contains substantially more than application source: documentation, tests, workflows, generated protocol artifacts, asset data, package/build scripts, and binary/static assets.

| Area | Current responsibility (high-level) | Audit status |
| --- | --- | --- |
| `core/` | Shared contracts, protocol types, provider interfaces, transport/persistence abstractions, shared utilities and asset logic | Partially inspected |
| `server/` | Agent lifecycle/runtime, provider integration, persistence, HTTP/WS API, terminal management, hooks, consent and usage reporting | Partially inspected |
| `adapters/vscode/` | VS Code extension activation, webview integration, agent management, terminal adapter, migration/uninstall | Partially inspected |
| `webview-ui/` | React UI, office rendering/interaction, editor, terminal UI, transport clients, mobile layout and music | Partially inspected |
| `e2e/` | Playwright end-to-end fixtures, helpers and tests for Claude hooks and standalone workflows | Tree inspected; implementation sampling pending |
| `scripts/` | Protocol generation/validation, packaging, asset tools, test reporting and build helpers | Tree inspected; implementation sampling pending |
| `.github/` | CI, publishing, dependency automation, issue/PR templates | Tree inspected; workflow review pending |
| `docs/` | ADRs, design/research notes, external asset guidance | Files identified; full review pending |
| Root configuration | npm workspace/build/test/lint configuration and contributor/security documentation | Partially inspected |

The inventory is a structural baseline, not proof that every tracked file has been reviewed line by line. Binary assets, generated output, lockfiles, and large fixture datasets require different review methods from handwritten source.

## 4. Existing architecture and dependency boundaries

### 4.1 Layering

**Verified from `CLAUDE.md`:**

The repository specifies these dependency constraints:

- `core/` depends on nothing.
- `server/` depends only on `core/`.
- `webview-ui/` depends only on `core/`.
- `adapters/vscode/` depends on `core/` and `server/`.
- The standalone CLI must not import the VS Code adapter, and the VS Code adapter must not depend on standalone-only code.

This is the most important existing architectural invariant identified so far. It keeps shared contracts independent of host/runtime implementation and allows the two client surfaces to reuse the same message model.

**Audit implication:** future architecture work should explicitly decide which of these boundaries survive, which are generalized, and which are replaced. A new engine must not become a dependency sink that silently absorbs UI, host-specific, or provider-specific behavior.

### 4.2 Core contracts

**Verified from inspected files:**

- `core/asyncapi.yaml` is documented as the AsyncAPI 3.0 protocol source of truth.
- `core/src/messages.ts` is generated; contributor instructions say not to edit it directly.
- `core/src/provider.ts` defines normalized `AgentEvent` events and the `HookProvider` boundary. It also defines usage-reporting/context types and a `UsageBridge` contract.
- `core/src/transport.ts` defines `MessageTransport`, including send, subscription, readiness, connection state, state-change subscription, and disposal.
- `core/src/adapter.ts` defines `StateAdapter` for agent/seat/settings persistence. Its comments explicitly state that layout persistence is outside this interface.
- `core/src/teamProvider.ts` defines the team-provider boundary (requires deeper inspection).

The core is therefore not an LLM reasoning engine. It is primarily a host-neutral contract/type and shared-logic layer.

### 4.3 Server and runtime

**Verified from inspected files:**

- `server/src/server.ts` exposes `PixelAgentsServer`, which owns or reuses a server instance and coordinates lifecycle/configuration.
- `server/src/httpServer.ts` creates a Fastify HTTP/WebSocket server.
- HTTP responsibilities include health, provider hook ingestion, usage reporting, WebSocket messaging, and terminal routes.
- The server supports embedded mode (VS Code) and standalone mode (SPA serving).
- Server discovery and multi-instance reuse are implemented through files under `~/.pixel-agents/`; reuse is capability-aware (embedded vs standalone and standalone access configuration).
- Embedded mode uses an ephemeral token; standalone mode loads or creates a persisted standalone token.
- The HTTP layer has authentication and WebSocket-origin/host checks. Exact security coverage needs a dedicated route-by-route review.
- Optional PTY/terminal management is provided to standalone mode; VS Code owns its own terminal integration.

The server currently acts as a host/runtime for tracking and controlling coding-agent sessions, not as a demonstrated general task scheduler/model gateway.

### 4.4 Provider integration

The provider boundary normalizes provider-specific events into a shared event model. The inspected provider interface explicitly describes HookProvider as the current shipped integration style and notes that other provider types should be derived from real provider requirements rather than speculative abstractions.

The README identifies Claude Code as supported today. The architecture notes in `CLAUDE.md` identify the Claude hook provider implementation under `server/src/providers/hook/claude/`.

**Audit implication:** provider event ingestion and model invocation are separate concerns. A future Model Gateway should not be conflated with the existing HookProvider interface. The current provider contract observes events from an external CLI; a model gateway would initiate and manage model requests.

## 5. Client and presentation architecture

### 5.1 Webview/browser UI

The UI is a React application (React 19 is indicated in the project dependency baseline) with substantial domain-specific code:

- office state, character entities and animation;
- Canvas-based office rendering and game loop;
- floor/wall/furniture layout and editor;
- character customization and asset handling;
- agent cards, launch flows, notifications and usage/context indicators;
- terminal interface and terminal input/selection behavior;
- music/radio subsystem;
- mobile-specific shell, gestures and viewport behavior;
- transport implementations for VS Code webview messaging and browser WebSocket.

The inspected renderer/game-loop files use Canvas 2D APIs and `requestAnimationFrame`. No evidence in the inspected files establishes a WebGL or full 3D renderer. Treat the visual layer as a 2D/2.5D pixel-art office unless further inspection demonstrates otherwise.

### 5.2 Transport implementations

The shared `MessageTransport` contract has at least two documented implementations:

- `PostMessageTransport` for VS Code webview messaging.
- `WebSocketTransport` for standalone browser connections, including reconnect states.

This separation is a useful portability seam. The protocol and transport contract should be evaluated before deciding whether the future Android/laptop clients need a new transport, a new protocol version, or only additional client implementations.

### 5.3 VS Code adapter

The adapter contains extension activation, webview view-provider integration, agent management, terminal adaptation, host-directory contribution, migration and uninstall behavior. It is an integration boundary, not the place for generic orchestration policy.

## 6. Build, dependencies and quality infrastructure

**Verified from root `package.json`:**

- TypeScript is the primary application language in the current repository.
- Build tooling includes esbuild and Vite.
- AsyncAPI generation is part of compile/package workflows.
- Type checking includes application and server test TypeScript projects.
- ESLint and Prettier are configured.
- Test commands cover webview tests, server tests, and package-contract tests.
- Playwright is used for end-to-end tests.
- Packaging scripts support VS Code/npm outputs.
- Fastify and its CORS/static/WebSocket plugins are root runtime dependencies.
- `@lydell/node-pty` is an optional dependency.

The test tree is substantial: server tests, webview unit tests, and Playwright E2E suites exist. However, test-file presence does not establish coverage quality, current pass status, or whether tests adequately protect the future refactor. No test command was executed as part of this document's preparation.

## 7. Existing documentation and sources of truth

Files identified on `main` include:

- `README.md`: product purpose, user-facing capabilities, setup and execution modes.
- `CONTEXT.md`: canonical glossary; defines domain vocabulary such as Host, Adapter, Agent, Sub-agent, Team, Lead, Teammate, Launch, Directory, Adopt, and Headless agent.
- `CLAUDE.md`: compressed contributor/agent reference, architecture layering, file ownership and implementation constraints.
- `core/asyncapi.yaml`: protocol contract source of truth.
- `docs/adr/0001-consent-choices-send-immediately-and-revise-as-absolute-state.md`
- `docs/adr/0002-protocol-follows-glossary.md`
- `docs/adr/0003-a-look-is-palette-and-repeat.md`
- `docs/design/standalone-terminal.md`
- `docs/external-assets.md`
- `docs/research/claude-harness-spawn-formats.md`

The existing ADRs and design/research documents must be read in full before any proposal supersedes their decisions. In particular, the glossary and protocol contract should be treated as authoritative within their declared scope.

The working branch also contains `docs/architecture/K41-HYBRID-LLM-ENGINE-DIRECTION.md`, which records the new direction as a directional design note, not an implementation specification. Its language and single-process modular-engine proposal remain provisional where explicitly marked.

## 8. Initial architectural strengths

These are positive observations, not claims that the system has no design problems:

1. **Explicit dependency direction.** The layering rules make intended boundaries legible.
2. **Protocol-first communication.** AsyncAPI generation reduces hand-maintained drift between message definitions and generated types.
3. **Provider normalization.** Provider-specific event formats are translated into a shared event model.
4. **Transport abstraction.** UI code can use different host transports behind one interface.
5. **Persistence abstraction.** Agent/seat/settings persistence has a replaceable interface.
6. **Two host surfaces share a runtime foundation.** Embedded and standalone modes reuse common server/runtime logic while retaining host-specific capabilities.
7. **Consent and user-data handling are treated as explicit design concerns.** The contributor reference describes guarded settings mutation, backup/verification, and consent-gated hook installation; these behaviors need preservation tests during any migration.
8. **Existing regression assets.** The repository has unit, server, package-contract, and E2E suites that can inform characterization tests.

## 9. Initial architectural risks and investigation targets

These are hypotheses or investigation targets, not confirmed defects.

| ID | Investigation target | Why it matters | Evidence needed |
| --- | --- | --- | --- |
| AUD-01 | Current runtime responsibilities and coupling | Future task orchestration may not fit cleanly into the current session runtime | Full dependency/call graph across server runtime modules |
| AUD-02 | Agent/session/task domain model | Current Agent and future worker/task concepts may have conflicting lifecycle semantics | Types, persistence schemas, lifecycle handlers, state transitions |
| AUD-03 | Protocol scope and compatibility | Existing AsyncAPI contract may be UI/session-centric | Full channel/message inventory and versioning rules |
| AUD-04 | Provider boundary | Hook observation is not equivalent to model invocation | Claude provider implementation, provider registry, team provider |
| AUD-05 | Persistence and recovery | Future task/worker state needs explicit durability and recovery guarantees | All persistence modules, schemas, write patterns, migration tests |
| AUD-06 | Terminal and tool execution security | Executing tools/commands requires clear authorization and isolation | PTY routes, consent gate, command launchers, auth, path handling |
| AUD-07 | Concurrency and lifecycle | Timers, watchers, PTYs, sockets and workers can create resource leaks or shutdown complexity | Lifecycle ownership map, shutdown paths, stress tests |
| AUD-08 | UI coupling to current domain | Reusing the office UI may constrain or accelerate the new product | UI message consumers, stores, hooks and component dependencies |
| AUD-09 | Configuration and environment assumptions | Host-specific filesystem and process assumptions affect portability | Config, paths, environment reads, OS-specific branches |
| AUD-10 | Test coverage and reliability | Large refactors need characterization and acceptance gates | Test execution, coverage, flaky test history, CI workflow |
| AUD-11 | Supply-chain and build complexity | Current package/build pipeline may not map to a native engine | Full workflows, lockfile/dependency audit, packaging |
| AUD-12 | Performance baseline | No evidence yet that current or proposed runtime meets target footprint | Reproducible CPU/RAM/startup/latency benchmarks |

## 10. Relevance to the proposed K41 Hybrid LLM Agent Engine

The new direction introduces capabilities that are not evidenced as first-class components in the inspected current code:

- Main Agent planning and decomposition;
- task graph/dependency management;
- bounded automated worker lifecycle;
- scheduling and concurrency policies;
- model/provider request gateway;
- context assembly, isolation and compaction;
- token/cost budgets and accounting;
- tool registry, policy enforcement and execution;
- task-level persistence, retry, cancellation and recovery;
- orchestration observability and benchmark instrumentation.

Some current components may be reusable or provide patterns (provider normalization, transport, consent, terminal management, UI, persistence, tests), but reuse must be justified by interface and lifecycle fit—not by matching names.

The current repository's `Agent` is defined as an AI coding session tracked by Pixel Agents. The future concept's Main Agent and automated workers may represent orchestration roles/tasks rather than terminal sessions. These concepts must be modeled distinctly unless a deliberate domain decision proves they should be unified.

## 11. Preliminary migration posture

Until the detailed audit is complete:

1. Preserve the current `main` branch as the baseline; do not use it as an experimental refactor branch.
2. Keep architecture direction documents separate from verified current-state findings.
3. Do not begin source-code migration based solely on this preliminary audit.
4. Do not delete existing features before classifying their user value, dependencies, data, and migration requirements.
5. Do not select libraries or runtime mechanisms before requirements and constraints are documented.
6. Do not claim C is faster/smaller than Rust without comparable measurements. C remains a leading candidate, not a benchmark-proven outcome.
7. Establish characterization tests and baseline measurements before replacing critical behavior.
8. Record irreversible or cross-cutting decisions as ADRs.

## 12. Audit completion checklist

This document is **not complete** until the following work is performed and its findings are incorporated:

- [ ] Read all root-level project, build, runtime, and contributor configuration files.
- [ ] Inspect every handwritten source module under `core/`.
- [ ] Inspect every handwritten source module under `server/src/`, including providers, runtime, persistence, hooks, terminal, HTTP and CLI.
- [ ] Inspect every handwritten source module under `adapters/vscode/`.
- [ ] Inspect the UI entry points, stores/state, transport, office engine, terminal, mobile and feature modules under `webview-ui/src/`.
- [ ] Read all existing docs and ADRs in full.
- [ ] Inspect scripts, asset pipeline, code generation and packaging.
- [ ] Review CI workflows and repository automation.
- [ ] Map tests to modules and execute the relevant baseline test/build commands where the environment permits.
- [ ] Build a module dependency map and identify actual dependency cycles or boundary violations, if any.
- [ ] Trace the full lifecycle of an agent from launch/adoption through events, state updates, persistence, reconnect and shutdown.
- [ ] Trace authentication, consent, hook installation, terminal command execution and sensitive operations.
- [ ] Record known limitations, unverified assumptions, and unresolved design questions.

## 13. Evidence and confidence convention

Use these labels in subsequent audit updates:

- **VERIFIED** — directly supported by inspected source, configuration, tests, or executed evidence.
- **INFERRED** — a reasoned conclusion from verified evidence; not directly stated by implementation.
- **PROPOSED** — a suggested future direction, not an accepted decision.
- **OPEN** — unresolved question requiring evidence or a decision.
- **NOT AUDITED** — identified but not yet inspected.
- **DEPRECATED** — superseded by a later explicit decision.

Do not upgrade an inference or proposal to a verified fact without new evidence.

---

## 14. Current audit status

**Completed in this preliminary pass:**
- Confirmed the `main` baseline commit and recursive tree response (`truncated: false`, 739 blobs).
- Identified the major repository areas and key documentation/source-of-truth files.
- Inspected selected root metadata, README, glossary, architecture reference, core contracts, provider abstraction, server lifecycle and HTTP server entry points.
- Inspected the current hybrid-engine direction note on the documentation branch.

**Still pending:** the full file-by-file source and documentation review, dependency/lifecycle mapping, test execution, and baseline measurements. This document must be updated as those tasks are completed.

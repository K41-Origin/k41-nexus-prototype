# K41 Hybrid LLM Agent Engine — Initial Direction

**Status:** Directional design note; not an implementation specification  
**Date:** 2026-11-24  
**Branch:** `docs/hybrid-llm-engine-direction`

## 1. Purpose

This document records the current product and architecture direction discussed for the next evolution of the project. It is intended to preserve decisions, assumptions, and open questions before implementation begins.

The intended system is a lightweight, resource-conscious **Hybrid LLM Agent System**. The design should optimize both local runtime efficiency and the efficiency of LLM orchestration, rather than treating executable size or RAM usage as the only measures of success.

## 2. Product concept: one Main Agent, specialized Automated LLM workers

The system is expected to have one **Main Agent** that coordinates work and multiple **Automated LLM workers** with bounded responsibilities.

### Main Agent

The Main Agent is the coordination point. Its expected responsibilities are:

- Receive and interpret user instructions.
- Decompose goals into tasks.
- Select appropriate workers and determine task dependencies.
- Coordinate task execution.
- Collect and evaluate worker results.
- Produce or approve the final response or outcome.

### Automated LLM workers

Workers handle narrowly scoped tasks, for example:

| Worker | Intended responsibility |
| --- | --- |
| Researcher | Gather relevant information |
| Analyzer | Analyze supplied information or data |
| Classifier | Classify input into a defined schema |
| Summarizer | Produce concise summaries |
| Validator | Check results against explicit criteria |
| Executor | Perform specifically authorized tool actions |

A worker should have explicit instructions, input/output contracts, token and time budgets, retry limits, permissions, and stopping conditions.

Workers do not necessarily need to be permanently running processes or independently hosted agents. The preferred initial concept is **task-based execution**: activate a worker when a task requires it, obtain a bounded result, then release its execution resources.

Deterministic work—such as parsing, filtering, simple routing, schema checks, and data transformations—should be handled by ordinary code where practical, not sent to an LLM by default.

## 3. Language direction: C as the leading candidate

C is the current leading candidate for the core engine.

Reasons for evaluating C include:

- Direct control over memory allocation and resource lifecycles.
- Low-level control over threads, processes, and operating-system integration.
- Ability to build a native runtime without requiring a large managed runtime.
- Suitability for compact, event-driven systems and native components.

The project is willing to accept a longer development and maintenance cycle where that is justified by the desired control and footprint. Development convenience is not the overriding selection criterion.

This is **not yet a final, benchmark-proven language decision**. C does not automatically use less memory or produce a smaller complete system than Rust. Actual results depend on architecture, implementation, libraries, allocator, build configuration, and workload. The choice must be validated with measurements.

Python may remain useful for tooling, experiments, prototyping, and asset/data pipelines, but is not currently proposed as the primary core runtime.

Rust remains a valid comparison candidate, especially for memory safety and concurrency, but is not the current leading direction.

## 4. Initial architecture direction

The proposed foundation is a **single-process, modular C engine**.

The initial module boundaries under consideration are:

| Module | Responsibility |
| --- | --- |
| Runtime | Initialization, shutdown, configuration, lifecycle |
| Scheduler | Task scheduling and concurrency limits |
| Agent Orchestrator | Coordination of Main Agent and workers |
| Task Engine | Task state, dependencies, cancellation, retries |
| Model Gateway | Provider/model integration and request handling |
| Context Manager | Context assembly, reduction, and isolation |
| Token Manager | Token budgets, usage accounting, and limits |
| Tool Engine | Tool registration, permission checks, and execution |
| Event Bus | Internal event delivery and module communication |
| State Manager | Runtime state and persistence boundaries |
| Network Layer | Communication with remote clients |

These are conceptual boundaries, not finalized APIs. Module interfaces, ownership rules, dependencies, and data structures still need to be specified.

### Single-process first

The initial design should keep modules inside one process and communicate through explicit internal interfaces and an event/queue mechanism.

This is preferred initially because it can reduce communication overhead, simplify deployment, and make resource ownership easier to reason about.

Multi-process execution and IPC may be introduced when there is a concrete requirement—for example, isolating untrusted tool execution or independently restarting a failure-prone component. We should not introduce microservices or process boundaries solely for architectural fashion.

## 5. LLM efficiency principles

The system must optimize the cost and amount of work performed by LLMs.

Initial principles:

- Give each worker a narrow, explicit task.
- Send only the context needed for that task.
- Define structured input and output contracts.
- Apply per-task token budgets and timeouts.
- Bound concurrency, retries, and recursive delegation.
- Avoid repeatedly sending unchanged context where caching or references are suitable.
- Prefer deterministic code for deterministic operations.
- Track token use and task outcomes so routing and worker design can be evaluated.

The key objective is not merely to create more workers. Workers should reduce the Main Agent's workload or improve task quality enough to justify their additional model calls.

## 6. Host and client direction

The core runtime should be independent of its user interface.

### Initial stage

- The program/host runtime runs on a laptop.
- The laptop provides monitoring and control.
- An Android application acts initially as a remote monitoring client and may later gain control capabilities.

### Later stage

- The host runtime may move to a private server.
- Laptop and Android clients may connect independently to that host.
- Both clients may eventually support monitoring and authorized control.

The Android client is not expected to bundle or run the full agent runtime in the initial stage. It should communicate with the host and display relevant state.

Remote access will require a deliberate security design, including transport encryption (TLS/WSS), authentication, device registration, authorization scopes/roles, session handling, reconnection behavior, rate limiting, and audit logging for sensitive actions. These are requirements to design, not features claimed to exist already.

## 7. Existing repository context and migration approach

The current repository, `K41-Origin/k41-nexus-prototype`, is based on Pixel Agents and already contains substantial functionality, including agent runtime/provider integration, hooks, terminal/PTy handling, persistence, session routing, HTTP/WebSocket transport, an AsyncAPI message contract, and a React/Canvas UI.

The existing architecture separates concerns across areas such as `core/`, `server/`, `webview-ui/`, and `adapters/`.

The proposed C engine should not trigger an automatic rewrite of the entire repository. Before changing existing components:

1. Map the existing capabilities and their dependencies.
2. Identify which components remain useful.
3. Define the new engine's responsibility and boundaries.
4. Separate orchestration concerns from UI and client transport.
5. Preserve or adapt compatible protocol and functionality where appropriate.
6. Replace components only where there is a demonstrated mismatch with the target architecture.

The earlier repository review was structural and selective, not a line-by-line audit of every file. No full migration or replacement decision has been made.

## 8. Candidate C ecosystem to evaluate

These are candidates for investigation, not selected dependencies:

| Concern | Candidates |
| --- | --- |
| HTTP client/server | libcurl, Mongoose |
| Event loop / I/O | libuv, platform-native facilities such as epoll where appropriate |
| JSON | yyjson, jsmn |
| WebSocket | libwebsockets |
| Persistence | SQLite |
| Allocator | system allocator first; alternatives such as mimalloc only if measurements justify them |
| Build | CMake |
| Testing | CTest, Unity |
| Profiling | Valgrind, perf, platform-appropriate profilers |

Avoid adopting all candidates by default. Selection should consider footprint, portability, maturity, maintenance, security, licensing, and integration cost.

## 9. Open architecture decisions

The following questions remain unresolved and must be answered during architecture and specification work:

1. **Worker execution lifecycle:** on-demand only, persistent services, or a hybrid model?
2. **Model topology:** one provider/model for all workers, or model/provider selection per task?
3. **Main Agent runtime:** which model/provider interfaces and control loop are required?
4. **Concurrency model:** event-loop driven, thread pool, or a measured combination?
5. **Memory model:** ownership conventions, allocation strategy, limits, and cleanup rules?
6. **Event system:** event types, queue semantics, backpressure, ordering, and failure behavior?
7. **Task lifecycle:** states, cancellation, deadlines, retries, dependencies, and recovery?
8. **Persistence:** what state must survive restart, and what storage is appropriate?
9. **Tool isolation:** which tools can run in-process and which require process isolation?
10. **Networking and security:** protocol, authentication, authorization, and remote-control boundaries?
11. **Client UI:** how much of the existing office visualization should be retained on Android and laptop?
12. **Performance targets:** acceptable idle/active RAM, CPU, startup time, binary/package size, latency, throughput, and concurrent task count?

## 10. Proposed development sequence

1. **Architecture:** define module responsibilities, dependencies, runtime model, concurrency, memory ownership, and event semantics.
2. **Technical specification:** define data structures, interfaces, task/worker lifecycles, configuration, logging, and error handling.
3. **Technology selection:** evaluate and select only the libraries needed by the specification.
4. **Core prototype:** implement runtime initialization/shutdown, event delivery, task scheduling, worker lifecycle, logging, configuration, and resource accounting.
5. **LLM integration:** add the Main Agent, worker definitions, Model Gateway, Context Manager, Token Manager, and controlled tool execution.
6. **Remote clients:** integrate laptop and Android clients using a secured host-client protocol.
7. **Profiling and optimization:** measure real workloads and optimize demonstrated bottlenecks.

## 11. Measurement principles

Optimization claims must be supported by measurements. At minimum, evaluate:

- Binary and packaged application size.
- Idle and active RAM.
- Idle and peak CPU.
- Startup and shutdown time.
- Task latency and throughput.
- Concurrent task capacity.
- Network overhead and reconnect behavior.
- Token consumption per task and end-to-end workflow.
- Reliability under cancellation, timeout, provider failure, and restart.

Measure local runtime efficiency separately from external LLM usage. A small local engine can still be inefficient if it makes redundant model calls, repeatedly sends large contexts, or schedules unnecessary workers.

## 12. Current summary

The current direction is to design a lightweight Hybrid LLM Agent System with one coordinating Main Agent and multiple bounded Automated LLM workers. C is the leading candidate for the core engine, and a single-process modular architecture is the preferred starting point. Workers should initially use task-based execution. The host runtime should remain separate from laptop and Android clients and should be deployable on a laptop first, with a private server as a later option.

This document records a **design direction**, not completed implementation, verified performance, or a frozen specification. All unresolved decisions remain open until supported by requirements, design review, and prototype measurements.

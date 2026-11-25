# Core Module Audit Record

**Status:** AUDITED — source inventory, source review and core documentation reverified statically.  
**Audit sessions:** `GOV-CORE-001` and `GOV-CORE-002` (2026-11-25)  
**Source branch snapshot:** `docs/hybrid-llm-engine-direction` at `c522dbdac525b226ec84afa5b7c829289ea1949f` (core source blob identities unchanged from the prior audited snapshot)  
**Historical inventory root:** `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1`  
**Inventory:** 28/28 tracked files; recursive tree `truncated: false`.  
**Evidence log:** [GOV-CORE-001](../EVIDENCE-LOG.md#2026-11-25--gov-core-001-core-source-and-documentation-re-verification)

## 1. Scope and method

Reviewed all files under `core/`:
- `core/asyncapi.yaml`
- 27 TypeScript files under `core/src/`, including generated `messages.ts`.

Re-read and cross-checked all five core documents:
- [README](../../core/README.md)
- [Module overview](../../core/MODULE-OVERVIEW.md)
- [File reference](../../core/FILE-REFERENCE.md)
- [Protocol](../../core/PROTOCOL.md)
- [Dependencies](../../core/DEPENDENCIES.md)

The recursive tree at the branch tip contains the same 28 blob SHAs as the historical core audit root. The exact source identity is recorded in the [evidence log](../EVIDENCE-LOG.md). The per-file responsibility and generated-source markers remain in [FILE-REFERENCE.md](../../core/FILE-REFERENCE.md).

## 2. Verified architectural role

`core/` is a shared host-neutral contract/domain layer. Its actual scope is broader than types:
- UI/server control-plane message contracts (AsyncAPI + generated TypeScript);
- provider-normalized event and capability interfaces;
- persistence, terminal discovery and message transport interfaces;
- shared persisted/wire shapes;
- character/profile validation, migration, identity remapping and palette selection;
- terminal frame parsing;
- pixel-art asset manifest/catalog/index generation and PNG decoding.

It does not implement server lifecycle, HTTP/WebSocket routes, filesystem persistence adapters, VS Code APIs, React rendering/state, PTY lifecycle, provider-specific hook parsing, or LLM inference/orchestration.

**Verified correction:** `core/src/index.ts` says the package is “types-only”, but exports runtime constants and the tree contains executable utilities and Node filesystem/PNG asset functions. This is a documentation/comment mismatch, not by itself a runtime defect.

## 3. Contract boundaries and dependencies

### Protocol
- `core/asyncapi.yaml` is the source of truth for generated `core/src/messages.ts`.
- AsyncAPI document version is pinned to 3.0.0; protocol metadata version is 1.0.0.
- Channel `/ws` carries ClientMessage/ServerMessage control-plane events.
- Raw terminal frames are separately defined in `terminalFrames.ts` and travel over the terminal WebSocket data plane.
- Schema shape does not establish authorization. Server-side authorization and runtime validation remain responsibilities of consumers.
- Generated `messages.ts` must not be manually edited.

### Host/provider contracts
- `HookProvider` normalizes provider-specific hook payloads into `AgentEvent`.
- `TeamProvider` is optional capability extension; it is not a generic orchestration-worker contract.
- `UsageBridge` defines consented account-usage reporting behavior.
- `StateAdapter`, `ITerminalAdapter`, and `MessageTransport` define interfaces; implementations and side effects belong to host layers.

### Domain/persistence
- `PersistedAgent` retains compatibility fields for old persisted forms.
- Character profile index is a palette index; stable IDs and `characterRemap` protect references across list reorder/insert/delete.
- Profile and wardrobe parsers are shared validators with bounded payload counts/fields; PNG semantic validity remains decoder/UI responsibility.
- `normalizeProjectPath` is a naming transform, not path canonicalization or security validation.
- `localId` uses Date.now/Math.random and is explicitly non-cryptographic.

### Asset runtime
- Pure transforms (color conversion, sheet/frame conversion, manifest flattening) coexist with Node-only `fs`/`path` operations and `pngjs` decoding.
- `pngDecoder.ts` uses `pngjs`; `build.ts` and `loader.ts` use Node filesystem/path APIs.
- The root manifest classifies `pngjs` as devDependency while core runtime source imports it. This is an **OPEN packaging/bundle verification question**, not a confirmed defect until bundle reachability and packaging are inspected.

## 4. Verified findings and observations

No new runtime bug is declared from this static pass. The following are recorded with evidence state:

| ID | Observation | Severity / confidence | State |
|---|---|---|---|
| CORE-DOC-01 | `core/src/index.ts` header says all package content is types-only, inconsistent with runtime exports and executable core modules. | Informational / High | Open documentation correction; source change not authorized |
| CORE-PKG-01 | Runtime import of `pngjs` vs root manifest devDependency classification requires bundle/package reachability check. | Potential Medium / Medium | Needs evidence; not confirmed defect |
| CORE-ARCH-01 | No upward imports from server/webview/adapters were identified in the audited core source. Architecture enforcement test was not identified in the core audit. | Informational / Medium | Needs cross-repository enforcement check |
| CORE-CONS-01 | Exact consumer mapping, local mirrored types and public export usage are not fully verified in core-only scope. | Informational / High | Open; resolve during layer audits |
| CORE-TEST-01 | Exact test-to-core-function coverage is unmapped; no core-specific test directory exists in the audited tree. | Informational / High | Open; inspect consumer test suites and run only when authorized |

These IDs are stable and must not be reused. The observations above are not all defects; the register distinguishes a verified mismatch from open verification questions.

## 5. Coverage map

| Sub-area | Source paths | Static source status | Cross-layer status |
|---|---|---|---|
| AsyncAPI + generated messages | `asyncapi.yaml`, `messages.ts` | Reviewed | Producers/consumers and generation execution need cross-layer/runtime evidence |
| Public barrel/constants | `index.ts`, `constants.ts` | Reviewed | Actual imports/public API reachability partial |
| Adapter/provider/team/transport contracts | `adapter.ts`, `provider.ts`, `teamProvider.ts`, `transport.ts`, `terminalAdapter.ts` | Reviewed | Implementations and lifecycle belong to server/adapters/webview audits |
| Shared schemas | `schemas.ts` | Reviewed | Persistence compatibility paths in server need correlated evidence |
| Character/domain utilities | `characterProfiles.ts`, `defaultCharacterProfiles.ts`, `directoryCharacters.ts`, `paletteUtils.ts`, `wardrobeItems.ts` | Reviewed | Server persistence and webview rendering/consumer behavior remain cross-layer |
| Small utilities | `isRecord.ts`, `localId.ts`, `normalizeProjectPath.ts` | Reviewed | Security/edge-case tests not executed |
| Asset pipeline | `assets/*` | Reviewed | Package/bundle, file fixtures, decoder behavior and host runtime remain unverified |
| Terminal frame contract | `terminalFrames.ts` | Reviewed | Route/client integration is covered by other module audits; runtime matrix not executed |

## 6. Explicit limitations

- No install, test, typecheck, lint, build, AsyncAPI validate/generate, package inspection, or runtime execution was performed in this audit session.
- No claim of test pass/fail.
- No claim of complete runtime consumer graph.
- No claim that all malformed binary/image cases have been exercised.
- No source changes were made.
- Static source review does not certify security or correctness.

## 7. Exit criteria for core

Core source/document audit is complete for this exact SHA snapshot. The module is not globally “closed” until these cross-layer questions are resolved or explicitly accepted:
1. Confirm `pngjs` is included in every production bundle/package path that reaches `pngDecoder.ts`.
2. Trace concrete consumers/implementations of public contracts, including VS Code and standalone.
3. Correlate profile/wardrobe validation and migration with persistence and UI decode paths.
4. Verify AsyncAPI generation/validation and generated-file parity through the authorized toolchain.
5. Map tests to core behavior and execute targeted tests when authorized.
6. Confirm architecture-boundary enforcement (or record the absence and accepted risk).

Do not reread all 28 files to answer these questions. Follow the relevant consumer, build, test, or generator path and reopen only the core lines implicated.

# E2E Deep Audit

**Status:** IN PROGRESS — inventory/SHA reconciliation complete (47/47); assertion-level review complete (21/21 spec files, 152/152 tests); four test-quality coverage gaps E2E-003–E2E-006 recorded; full static review of 25/25 fixture/helper/setup/config files completed (GOV-E2E-003); cross-module review remains open.  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before this publication:** `docs/hybrid-llm-engine-direction@cbf2d7ed7f711bc44fb619e43d86c560e1cf7d7e`.  
**Inventory:** 47/47 blobs under `e2e/`; 7 directories; 0 missing, 0 extra, 0 SHA mismatches against the documentation-branch snapshot.  
**File-level owner:** [FILE-REFERENCE.md](FILE-REFERENCE.md)  
**Governance owner:** [modules/e2e.md](../governance/modules/e2e.md)

## 1. Governance and scope

This audit follows the repository governance hierarchy and SHA-first continuation method:
- governance README, audit register, audit method and append-only evidence log;
- module records for core, server, webview-ui and adapters;
- existing E2E architecture README and file-reference template;
- source branch and documentation branch recorded separately.

**Scope:** all tracked files beneath `e2e/`, including the suite README, fixtures, mock process shims, test helpers, setup, Playwright config, TypeScript config and all test specs.

**Exclusions:** `scripts/`, `.github/`, package/release implementation beyond targeted manifest references, application-source re-audit, test/build/lint/typecheck execution and runtime experiments. These remain separate workstreams.

## 2. Repository and inventory verification

The recursive source tree at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` returned `truncated=false`. It contains:
- **47 blobs** under `e2e/`;
- **7 tree entries**: `fixtures/`, `helpers/`, `tests/`, `tests/claude/`, `tests/claude/hooks-off/`, `tests/claude/hooks-on/`, `tests/standalone/`.

All 47 source blobs were fetched individually. A second recursive tree at the documentation branch's tree object `1883eeea8af8fc8217d7d279be6ab34ab4d91bb6` also returned `truncated=false`. Comparing `(path, blob SHA)` pairs for `e2e/`:
- source branch: 47 blobs;
- documentation branch: 47 blobs;
- added: 0; removed: 0; changed SHA: 0.

The complete path, mode, size and SHA ledger is in [FILE-REFERENCE.md](FILE-REFERENCE.md). Tree reconciliation establishes identity and completeness, not review completion.

## 3. Test inventory

The 21 spec files contain **152 executable `test()` declarations**, and all 152 carry an `@area:` tag. This source count agrees with the generated inventory in `e2e/README.md`. There are also **8 platform skip guards** (these are guards, not additional test declarations):
- Windows PTY/mock-shim guards in standalone character-roster, conversations, launch-drawer, mobile and terminal suites;
- a Windows guard for the chmod-based hook-uninstall failure scenario.

The suite README groups the tests into spawn, lifecycle, cross-cutting, teams, hooks-off matrix, standalone, pets, carpet, areas, editor and mobile areas. The test files are split between VS Code-hosted tests (`fixtures/pixel-agents.ts`) and standalone browser/CLI tests (`fixtures/standalone.ts`).

**Current test evidence:** `TEST-INVENTORIED` — assertions and their immediate setup/wait conditions reviewed in all 21/21 spec files (152/152 test cases). This is not test execution. No test has been executed in this audit.

## 4. Harness architecture observed

### VS Code extension lane
- `global-setup.ts` obtains a VS Code executable through `@vscode/test-electron`, coordinates downloads with a directory lock, and patches the downloaded Windows `product.json` to avoid the InnoSetup update mutex.
- `helpers/launch.ts` creates isolated temporary HOME/workspace/user-data/bin directories, seeds product config/layout and Claude settings, enables Agent Teams, installs the mock CLI shim, configures an isolated VS Code profile and launches the extension development host through Playwright Electron.
- `fixtures/pixel-agents.ts` waits for the workbench, opens and arranges the Pixel Agents panel, captures a webview frame, starts the narration monitor and attaches diagnostic artifacts on failures.
- The fixture teardown attempts to finish narration, terminate tracked external mock processes, close VS Code and remove the isolated temp tree.

### Standalone lane
- `helpers/standalone.ts` starts the local built `dist/cli.js`, allocates a loopback port, waits for health and for the CLI-printed URL, then opens that URL in the Playwright page.
- The URL printed by the CLI is deliberately used as the browser capability channel; the fixture does not synthesize a privileged URL from the server registry.
- The standalone fixture records incoming JSON WebSocket messages, attaches host logs and cleans up owned HOME/workspace directories.
- Standalone tests use the same helper surfaces where possible, but they are not equivalent to tests against a published npm package or a real Claude installation.

### Mock and observability
- `fixtures/mock-claude`, `mock-claude.cmd` and `mock-claude-runner.cjs` simulate a process-boundary CLI, append-only JSONL transcripts and installed hook invocation.
- `helpers/mock-claude.ts` builds timed scenarios, queues them for mock invocations and tracks detached external mock processes.
- `helpers/hooks.ts` probes the hook server and sends authenticated hook events; direct hook POSTs are used by standalone tests where there is no VS Code terminal to host a mock process.
- `helpers/editor.ts`, `helpers/office.ts` and webview test hooks expose deterministic state assertions. Some tests intentionally bypass canvas pixel-to-grid geometry or use state hooks; those assertions verify the invoked application handlers/state, not the full physical pointer path.
- `helpers/test-narration.ts` and `fixtures/tail-follow.cjs` provide explicitly cosmetic run narration. The README states narration must not gate behavior or assertions.


## 5. Assertion-level review — GOV-E2E-002

All 21 spec files were revisited at assertion level. The review examined each test declaration, direct Playwright assertions, state-hook reads, wait/poll conditions, persistence checks, negative assertions, and the extent to which UI gestures are real versus test-hook shortcuts. The 152 tests all carry area tags. The detailed file identities remain in [FILE-REFERENCE.md](FILE-REFERENCE.md); the table below records review scope without duplicating every assertion.

| Spec file | Assertion focus | Status |
|---|---|---|
| `claude/hooks-off/areas-multiroot.spec.ts` | Area CRUD, folder mapping, seat preference, Directory modal mapping and rename | TEST-INVENTORIED |
| `claude/hooks-off/areas.spec.ts` | Seeded area state, showAreas and Areas toolbar gate | TEST-INVENTORIED; see E2E-003 |
| `claude/hooks-off/carpet.spec.ts` | Sprite broadcast, tile painting, marching-squares mask, erase, eyedropper, undo, save/reload, surface overlap | TEST-INVENTORIED; see E2E-004 |
| `claude/hooks-off/editor.spec.ts` | Erase/furniture semantics, undo boundaries, desk rider move/copy/rotation, fit validation and colour copy | TEST-INVENTORIED |
| `claude/hooks-off/launch-drawer.spec.ts` | Host/user Directory rows, launch origin labels and reload | TEST-INVENTORIED |
| `claude/hooks-off/lifecycle.spec.ts` | Clear/resume reassignment, stale cleanup, parallel subtasks, teammate removal, timers and turn-end | TEST-INVENTORIED |
| `claude/hooks-off/matrix.spec.ts` | Internal/external × basic/inline/tmux discovery and activity routing | TEST-INVENTORIED |
| `claude/hooks-off/pets.spec.ts` | Sprite manifest, placement/removal, persistence/reload and click bubble | TEST-INVENTORIED; see E2E-006 |
| `claude/hooks-on/basic.spec.ts` | Internal spawn/subtask and external hook adoption, permission/idle/session-end lifecycle | TEST-INVENTORIED |
| `claude/hooks-on/consent.spec.ts` | Consent choices, install/uninstall, migration, third-party preservation and failure handling | TEST-INVENTORIED |
| `claude/hooks-on/lifecycle.spec.ts` | Clear/resume, team/subagent lifecycle, sound, status mapping, hook persistence and editor round-trip | TEST-INVENTORIED |
| `claude/hooks-on/teams.spec.ts` | Inline/tmux/new-harness teammate routing, unnamed subagent and lifecycle | TEST-INVENTORIED |
| `standalone/characters.spec.ts` | Profile save/undo/redo/discard, wardrobe item export/save/reload and roster launch | TEST-INVENTORIED |
| `standalone/conversations.spec.ts` | Conversation title/list/search/resume, identity/seat continuity and Directory sections | TEST-INVENTORIED |
| `standalone/directory-modal.spec.ts` | Keyboard semantics, no-save cancellation, path save and Area mapping persistence | TEST-INVENTORIED |
| `standalone/hooks.spec.ts` | Hook lifecycle messages, context reset, token boundary, spectator mode and consent | TEST-INVENTORIED |
| `standalone/launch-drawer.spec.ts` | Host/user Directories, deletion, validation, suggestions, settings sync and viewport constraints | TEST-INVENTORIED |
| `standalone/mobile.spec.ts` | Phone navigation, conversation resume, link handling, keyboard, clipboard, touch gestures, trackpad and roster | TEST-INVENTORIED; see E2E-005 |
| `standalone/multi-server-hooks.spec.ts` | Extension/standalone hook-server isolation and cross-contamination | TEST-INVENTORIED |
| `standalone/terminal.spec.ts` | PTY lifecycle, input/output, reconnect, card sync, copy/reflow, URL handling and no-terminal mode | TEST-INVENTORIED |
| `standalone/ui.spec.ts` | Close, diagnostics, external assets, import/export, reconnect and narrow toolbar hit targets | TEST-INVENTORIED |

### Assertion-quality findings

#### E2E-003 — seeded-layout Areas gate assertion is not isolated

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING / TEST-INVENTORIED
- **Evidence:** `e2e/tests/claude/hooks-off/areas.spec.ts:92-103`, blob `4b6b96fdee7634361bdc0b754c13038fb07bcf1d`.
- **Trigger:** the test claims to verify that a seeded layout's Area definition makes the Areas tool available.
- **Technical explanation:** the assertion only checks that the toolbar button exists. The test itself documents that the host always contributes at least one workspace Directory, and the production gate is an OR between seeded layout areas and mappable directories. Therefore the workspace Directory alone can satisfy the assertion even if the seeded-layout branch is broken.
- **Impact:** a regression in the seeded-layout gate can pass this test; its name/comment must not be interpreted as isolated evidence for that branch.
- **Remaining uncertainty:** no application behavior is claimed broken. This is an assertion-isolation/coverage gap; an isolated fixture or a lower-level contract test would be needed to prove the seeded-layout arm independently.

#### E2E-004 — carpet/furniture coexistence assertion does not prove co-location

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING / TEST-INVENTORIED
- **Evidence:** `e2e/tests/claude/hooks-off/carpet.spec.ts:297-318`, blob `668eff0627a55adaf1682c3650c088321ba7fa81`.
- **Trigger:** the test is intended to prove a seeded carpet tile and furniture coexist at the same coordinate `(3,3)`.
- **Technical explanation:** it waits for the carpet at `(3,3)`, then asserts only `getFurnitureCount() >= 1`. It never reads furniture coordinates or verifies that a furniture item occupies `(3,3)`.
- **Impact:** the test can pass if the carpet exists and unrelated furniture exists elsewhere; overlap/coexistence at the same tile is not actually asserted.
- **Remaining uncertainty:** the fixture seeds furniture at `(3,3)`, but the assertion does not independently establish that the seeded object loaded there.

#### E2E-005 — mobile hooks-info modal test omits vertical viewport bounds

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING / TEST-INVENTORIED
- **Evidence:** `e2e/tests/standalone/mobile.spec.ts:417-439`, blob `bc2cb73f0f7e63850bc2edd61a82ec2c6034245b`.
- **Trigger:** the test claims that the hooks information modal fits the phone screen.
- **Technical explanation:** for the title, Got it button and Close button it asserts only horizontal bounds (`x >= 0` and `x + width <= PHONE.width`). No assertion checks `y >= 0` or `y + height <= PHONE.height`.
- **Impact:** a vertically clipped/off-screen modal can still pass despite the test title and stated coverage.
- **Remaining uncertainty:** this is an assertion coverage gap; no runtime clipping has been observed.

#### E2E-006 — pet persistence test checks count, not identity or type

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING / TEST-INVENTORIED
- **Evidence:** `e2e/tests/claude/hooks-off/pets.spec.ts:147-197`, blob `37cc965cf3e73bea101553e68d325419704ff17e`.
- **Trigger:** the test claims both Claudio and Gitcat persist through disk save and panel reload.
- **Technical explanation:** it confirms the disk array has length two and the reloaded live pet array has length two, but it does not assert the persisted/reloaded pet IDs, names or `petType` values. The preceding single-pet check proves Claudio's type only before the second placement.
- **Impact:** two pets of the wrong/duplicated type could satisfy the persistence assertions; the test proves cardinality, not identity-preserving round-trip.
- **Remaining uncertainty:** no incorrect pet restoration is observed; the test's assertion contract is weaker than its stated coverage.


## 6. Findings

### E2E-001 — standalone process termination fallback is unreachable after SIGTERM is sent

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `e2e/helpers/standalone.ts:188-201`, blob `ab94998af9c08316a126ef1c2b0ca8f8c269cb17`.
- **Related evidence:** `e2e/helpers/mock-claude.ts:325-340`, blob `90754501c25ee2b7af4720b00646b557eade48e9`.
- **Trigger:** the standalone child process does not exit within the two-second SIGTERM wait.
- **Technical explanation:** Node's `ChildProcess.killed` indicates that a signal was successfully sent; it does not establish that the process has exited. After `child.kill('SIGTERM')`, `child.killed` becomes true when the signal was sent. If the two-second race expires while `exitCode` is still null, the fallback condition `child.exitCode === null && !child.killed` is false, so SIGKILL is not sent. A subsequent call also returns early when `child.killed` is true even if the process has not exited.
- **Impact:** a hung standalone host can survive fixture cleanup, retain the test port and continue using files after the fixture removes its temporary HOME/workspace. This can make reconnect/restart tests flaky and contaminate later runs.
- **Related teardown concern:** `killTrackedExternalProcesses()` sends SIGTERM and immediately clears its registry without waiting for exit. It likewise does not prove those mock processes have terminated before the owning fixture removes its temporary directory. This is recorded as corroborating lifecycle evidence, not a separate ID.
- **Remaining uncertainty:** no process was intentionally made to ignore SIGTERM; the exact orphaning behavior depends on the child process and platform. Runtime reproduction and a process-tree check remain OPEN.

### E2E-002 — standalone test port allocation has a close-before-bind race

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `e2e/helpers/standalone.ts:84-101`, blob `ab94998af9c08316a126ef1c2b0ca8f8c269cb17`.
- **Trigger:** another local process binds the selected loopback port after the temporary server closes its port-0 listener and before `spawnStandaloneHost()` binds the CLI to that port.
- **Technical explanation:** `getFreePort()` asks the OS for an ephemeral port, closes the listening socket, then returns the now-unreserved port. The CLI is started later as a separate process; there is no reservation held across that interval and no retry around an address-in-use startup failure.
- **Impact:** occasional standalone E2E startup failures under concurrent local services or parallel test invocations.
- **Remaining uncertainty:** no collision was induced; practical frequency is unknown.

## 7. Full static harness source review — GOV-E2E-003

The complete contents of all 25 non-spec harness files were reviewed at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; each current blob SHA matches the FILE-REFERENCE ledger. Scope was all six fixture/shim files, all 15 helpers, `global-setup.ts`, `playwright.config.ts`, `run-config.ts` and `tsconfig.json`. No source file was modified and no command was executed. Exact per-file SHA and line-count evidence is recorded in FILE-REFERENCE.

### Harness behavior and boundaries verified

- **Scenario queue and mock process:** `ClaudeMockScenarioBuilder` serializes typed action/session definitions to a per-HOME JSON queue; the CJS runner claims one queued scenario, creates Claude project/transcript/sidecar files, schedules JSONL writes/hooks/file actions and optionally exits or holds open. Hook execution uses the installed hook command and sends JSON on child stdin. POSIX and Windows wrappers invoke the same runner; narration is explicitly cosmetic.
- **VS Code lane:** isolated HOME/workspace/user-data/bin trees are created; config/layout/Claude settings are seeded before activation; multi-root workspaces are generated; the mock CLI and sibling scripts are copied; platform-specific environment/profile settings are applied; Electron launches with the extension dev path and the fixture attaches failure diagnostics. Fixture teardown finishes narration, attempts external-process termination, closes VS Code and removes the temporary root.
- **Standalone lane:** the fixture owns only directories it creates, seeds consent/config/layout when appropriate, starts local `dist/cli.js`, waits for health and the printed browser capability URL, records WebSocket messages, and exposes stop/restart/cleanup. The browser URL is sourced from CLI stdout rather than reconstructed from the privileged server registry.
- **Hook and test helpers:** hook server readiness is polled via server.json + health endpoint; event POSTs include the bearer token. Editor/office helpers explicitly distinguish real canvas gestures from test-hook calls that bypass pixel geometry or gestures. Transcript/team helpers construct JSONL and sidecar fixtures. Narration and terminal tail are isolated from assertions by design.
- **Orchestration/configuration:** Playwright uses a single worker by default, fullyParallel test distribution and one retry; Electron recording is configured separately from browser-page video. Run-ID namespacing is shared by report/output paths. TypeScript configuration is strict.

### New static findings

#### E2E-007 — run ID `..` escapes the artifact namespace and broadens cleanup target

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** `e2e/run-config.ts:3-19`, blob `1921baad5195765b91a6661afd2f75415c49be96`; cleanup sink `e2e/global-setup.ts:7,136-137`, blob `bb0b9611947b1ebaccea2cdd186811d2ab898176`.
- **Trigger / precondition:** the environment variable `PIXEL_AGENTS_E2E_RUN_ID` is set to exactly `..` (or another value that sanitizes to a parent segment).
- **Technical explanation:** the sanitizer permits dots and does not reject the special path segment `..`. `namespaceE2EPath(baseDir)` passes that segment directly to `path.join(baseDir, runId)`, which resolves to the parent of `baseDir`. `globalSetup()` then recursively removes `ALLURE_RESULTS_DIR`; with this run ID, the cleanup target resolves from `allure-results/e2e/..` to the broader `allure-results` directory. The same namespace function also broadens Playwright output/report paths.
- **Impact:** a malformed or misconfigured run-ID environment variable can cause E2E startup to recursively delete report data outside the intended per-run `e2e/<runId>` directory.
- **Remaining uncertainty:** no deletion or runtime experiment was performed; this is a static path-resolution consequence. The environment's actual source and likelihood are unknown.

#### E2E-008 — VS Code download lock has no stale-owner recovery

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** `e2e/global-setup.ts:24-72`, blob `bb0b9611947b1ebaccea2cdd186811d2ab898176`.
- **Trigger / precondition:** a process creates `download.lock` and terminates abnormally before the `finally` block removes it.
- **Technical explanation:** lock acquisition writes `owner.json` with PID/time, but contention handling only polls for directory disappearance. It never reads the owner record, checks whether the PID remains alive, or expires stale ownership; a later run therefore waits until the fixed ten-minute deadline and fails.
- **Impact:** a crashed/interrupted VS Code download can leave a persistent cache lock that blocks subsequent E2E setup on a reused workspace/cache.
- **Remaining uncertainty:** impact depends on cache persistence and abnormal process termination; no stale lock was created or tested.

### Review result and retained findings

- Full static source review: **25/25** fixture/helper/setup/config files.
- Assertion review: retained **21/21** spec files / **152/152** test declarations as GOV-E2E-002 evidence; not repeated here.
- Findings **E2E-001–E2E-008** remain OPEN. E2E-001/E2E-002 process/port issues and E2E-003–E2E-006 assertion gaps are preserved unchanged; E2E-007/E2E-008 are new and distinct.
- No test/build/lint/typecheck/install/browser/VS Code/PTY/runtime experiment was executed.

## 8. Cross-module integration and coverage boundaries

The E2E layer is a consumer of, not a source of truth for, application contracts. Preserve these canonical findings and continue them in their owning records:

| E2E touchpoint / gap | Existing owner and IDs | Audit interpretation |
|---|---|---|
| VS Code host activation, agent restore/removal and terminal/webview disposal | adapters: A-03, A-05, A-06; server: F-01, F-03, F-05 | E2E contains lifecycle and panel-reload scenarios, but does not establish all activation/deactivation, repeated-view, shutdown and timer-cancellation invariants. |
| Standalone reconnect, snapshot replay and queued commands | webview: WV-RESYNC-01, WV-RESYNC-02, WV-APP-01, WV-TR-01 | `standalone/ui.spec.ts` checks the connection indicator across a host stop/restart; it does not fully assert stale-seat reconciliation or classification/cancellation of queued ClientMessages. |
| Multi-client consistency and shared-state fan-out | webview: WV-PROTO-01, WV-PROTO-02, WV-PROTO-03 | Current E2E does not provide a comprehensive two-client consistency matrix for shared mappings/assets/config mutations. The README also defers producer/viewer relay scenarios. |
| Runtime message validation and authorization | core AsyncAPI contract; server F-08; webview WV-C08 | E2E exercises selected valid and invalid user flows, but is not a complete malformed-payload/schema-validation matrix. Reuse the completed protocol audit; do not duplicate it here. |
| CLI/package distribution | root `package.json`, scripts and CI/release | The standalone fixture launches local `dist/cli.js`; this does not by itself prove the published npm package installation path. Continue under scripts/package/CI governance. |

### Explicit suite gaps already documented in `e2e/README.md`

- multi-window `layout.json` cross-sync;
- external asset directory add/remove through Settings;
- bypass-permissions startup flag;
- workspace-folder add/remove during a session;
- internal-terminal heuristic-timer cancellation race (external-agent variant is covered);
- real mobile keyboard, long-press, copy-selection, flick-scroll, pinch and card-reorder feel on devices;
- producer/viewer relay and reconnect reconciliation.

The README's pre-release manual smoke also calls out real Claude JSONL/MCP behavior, Firefox/Safari standalone behavior, cross-window sync, first-run behavior and platform sanity. These are manual checks, not automated E2E evidence.

## 9. Verification boundaries

- **TREE-VERIFIED:** complete source recursive tree and all 47 E2E paths/modes/sizes/blob SHAs.
- **SHA-RECONCILED:** all 47 E2E path/blob-SHA pairs match between source and documentation branch snapshots.
- **FETCHED:** all 47 blobs fetched individually.
- **TEST-INVENTORIED:** assertion-level review complete for 21/21 spec files; 152 declarations and 152 area tags match the generated README inventory.
- **STATIC-FINDING:** E2E-001, E2E-002, E2E-007 and E2E-008; assertion-quality STATIC-FINDINGs E2E-003–E2E-006.
- **TEST-EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.

No install, test, build, lint, typecheck, browser, VS Code host, PTY, hook fault injection or runtime experiment was performed. No application source, test, dependency, package manifest or configuration was modified. Documentation changes are confined to the audit branch.

## 10. Continuation plan

1. Preserve TEST-INVENTORIED status for all 21 spec files; address E2E-003–E2E-006 in a future test-quality improvement pass if implementation changes are authorized.
2. Finish full source review of every fixture/helper/configuration file and trace the scenario queue, child-process lifetime, temporary-directory ownership, artifact paths and failure cleanup end to end.
3. Cross-check test hooks and key test paths against the relevant source in webview-ui, server and adapters, reopening only files needed for a named E2E contract.
4. Inspect `scripts/run-e2e.mjs`, `scripts/generate-e2e-inventory.mjs`, report generation and CI workflows under the scripts and `.github/` audit scopes.
5. Keep E2E-001/E2E-002 open until source change plus appropriate authorized verification evidence exists. No runtime execution is authorized by this audit record.

# E2E Deep Audit

**Status:** IN PROGRESS — complete tree inventory and SHA reconciliation; all 47 blobs fetched; 21 spec files structurally inventoried (152 test declarations); assertion-level and cross-module review continues.  
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

The 21 spec files contain **152 test declarations**, and all 152 carry an `@area:` tag. The source count agrees with the generated inventory in `e2e/README.md`. There are also **8 platform skip guards** (these are guards, not additional test declarations):
- Windows PTY/mock-shim guards in standalone character-roster, conversations, launch-drawer, mobile and terminal suites;
- a Windows guard for the chmod-based hook-uninstall failure scenario.

The suite README groups the tests into spawn, lifecycle, cross-cutting, teams, hooks-off matrix, standalone, pets, carpet, areas, editor and mobile areas. The test files are split between VS Code-hosted tests (`fixtures/pixel-agents.ts`) and standalone browser/CLI tests (`fixtures/standalone.ts`).

**Current test evidence:** `FETCHED / STRUCTURAL INVENTORY`. Assertion-by-assertion review is not yet complete. No test has been executed in this audit.

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

## 5. Findings

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

## 6. Cross-module integration and coverage boundaries

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

## 7. Verification boundaries

- **TREE-VERIFIED:** complete source recursive tree and all 47 E2E paths/modes/sizes/blob SHAs.
- **SHA-RECONCILED:** all 47 E2E path/blob-SHA pairs match between source and documentation branch snapshots.
- **FETCHED:** all 47 blobs fetched individually.
- **TEST-INVENTORY:** 21 spec files structurally counted; 152 declarations and 152 area tags match the generated README inventory.
- **STATIC-FINDING:** E2E-001 and E2E-002.
- **TEST-EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.

No install, test, build, lint, typecheck, browser, VS Code host, PTY, hook fault injection or runtime experiment was performed. No application source, test, dependency, package manifest or configuration was modified. Documentation changes are confined to the audit branch.

## 8. Continuation plan

1. Complete assertion-level review of all 21 spec files, keeping the per-file status in [FILE-REFERENCE.md](FILE-REFERENCE.md). Do not infer test quality from test names or the README inventory alone.
2. Finish full source review of every fixture/helper/configuration file and trace the scenario queue, child-process lifetime, temporary-directory ownership, artifact paths and failure cleanup end to end.
3. Cross-check test hooks and key test paths against the relevant source in webview-ui, server and adapters, reopening only files needed for a named E2E contract.
4. Inspect `scripts/run-e2e.mjs`, `scripts/generate-e2e-inventory.mjs`, report generation and CI workflows under the scripts and `.github/` audit scopes.
5. Keep E2E-001/E2E-002 open until source change plus appropriate authorized verification evidence exists. No runtime execution is authorized by this audit record.

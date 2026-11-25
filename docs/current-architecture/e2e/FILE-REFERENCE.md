# E2E File Reference

**Status:** INVENTORY COMPLETE — 47/47 tracked blobs reconciled and individually fetched at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. Static/test review is still in progress; fetched does not mean reviewed or executed.  
**Tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated=false`.  
**Reconciliation:** 47/47 path + blob SHA match against the documentation branch snapshot; 0 missing, 0 extra, 0 mismatched.  
**Detailed findings:** [DEEP-AUDIT.md](DEEP-AUDIT.md).

## Status vocabulary

- `TREE-VERIFIED`: path, mode, size and blob SHA present in the complete Git tree.
- `SHA-RECONCILED`: path/blob SHA agrees with the comparison snapshot.
- `FETCHED`: file content was retrieved; this alone is not a review.
- `FETCHED / TEST STRUCTURE INVENTORIED`: test declarations, area tags and major assertion/wait surfaces were structurally catalogued; assertion-level review remains open.
- `SOURCE-VERIFIED`: complete static review with line evidence; do not assign until completed.
- `TEST-INVENTORIED`: assertion-level review completed; does not mean test executed.
- `TEST-EXECUTED`: only after an authorized run with command/environment/exit code recorded.
- `OPEN`: further evidence or review remains.

All rows below are `TREE-VERIFIED`, `SHA-RECONCILED` and `FETCHED` at the stated source snapshot. The Status column records the additional review state.

## Complete inventory

| Path | Mode | Bytes | Blob SHA | Status | Responsibility |
|---|---:|---:|---|---|---|
| `e2e/README.md` | `100644` | 38245 | `f2655835d6f5ff038e23a5a023f72de351b8a829` | DOC-RECORDED | Suite scope, coverage gaps, manual smoke and generated 152-test inventory. |
| `e2e/fixtures/mock-claude` | `100755` | 1046 | `9d6721fadd18d0610cfcf9aa06111d86d62c0daf` | FETCHED / REVIEW IN PROGRESS | Executable POSIX mock CLI wrapper. |
| `e2e/fixtures/mock-claude-runner.cjs` | `100644` | 17517 | `6072b23f637ec2cd3518965f0fabc97f230e9051` | FETCHED / REVIEW IN PROGRESS | Scenario execution, JSONL and hook process simulation. |
| `e2e/fixtures/mock-claude.cmd` | `100644` | 1101 | `94806fde99023ccfb79db8c24236fb7260532613` | FETCHED / REVIEW IN PROGRESS | Windows mock CLI wrapper. |
| `e2e/fixtures/pixel-agents.ts` | `100644` | 8324 | `f4239fecbba1d6e9b09d3e3c224249592251db20` | FETCHED / REVIEW IN PROGRESS | VS Code/Electron fixture lifecycle and artifacts. |
| `e2e/fixtures/standalone.ts` | `100644` | 3386 | `0007a8c1947d0ac702093ef3040308895b861d5b` | FETCHED / REVIEW IN PROGRESS | Standalone browser fixture lifecycle and artifacts. |
| `e2e/fixtures/tail-follow.cjs` | `100644` | 3483 | `61a48c46957d121b6db4fb15f058dc16a208d985` | FETCHED / REVIEW IN PROGRESS | Cross-platform cosmetic narration tail. |
| `e2e/global-setup.ts` | `100644` | 5399 | `bb0b9611947b1ebaccea2cdd186811d2ab898176` | FETCHED / REVIEW IN PROGRESS | VS Code download/cache setup and Windows product patch. |
| `e2e/helpers/allure-labels.ts` | `100644` | 1537 | `eea3551753d78d5805df92b4bdfbebf874a02310` | FETCHED / REVIEW IN PROGRESS | Maps area tags to Allure epics. |
| `e2e/helpers/editor.ts` | `100644` | 16320 | `7ef4f0671ad5a9c87e34a612b6dd549033390c5e` | FETCHED / REVIEW IN PROGRESS | Editor/carpet/area test actions and state readers. |
| `e2e/helpers/external-monitor.ts` | `100644` | 837 | `696f0fa9cd3787611697d8163293da0dd8839876` | FETCHED / REVIEW IN PROGRESS | External mock narration log path. |
| `e2e/helpers/hooks.ts` | `100644` | 7535 | `81b5bd497a5f16a72deb79aa97410b166cf96345` | FETCHED / REVIEW IN PROGRESS | Hook-server probe, event POST and payload builders. |
| `e2e/helpers/internal-agent.ts` | `100644` | 7568 | `2a9a043c6b02f38b71cccf8bed6a2f10aa3bee78` | FETCHED / REVIEW IN PROGRESS | Internal agent launch and transcript discovery helpers. |
| `e2e/helpers/intro.ts` | `100644` | 1839 | `f973a12a6b05805cd0a8ce971d729a72967aae9e` | FETCHED / REVIEW IN PROGRESS | First-run intro and consent flow helpers. |
| `e2e/helpers/launch.ts` | `100644` | 22295 | `a67cddb2979897921184c232bc8d81c725d1995c` | FETCHED / REVIEW IN PROGRESS | VS Code host, isolated environment, mock CLI and cleanup. |
| `e2e/helpers/layout-seed.ts` | `100644` | 4207 | `7669eb198d61f08489dae53eb715402f6fd8cf4f` | FETCHED / REVIEW IN PROGRESS | Seed builders for layout and config. |
| `e2e/helpers/lifecycle.ts` | `100644` | 1679 | `d58a34ca1c83d93841c60611962eb3b115e92a5b` | FETCHED / REVIEW IN PROGRESS | Teammate session and team fixture builders. |
| `e2e/helpers/mock-claude.ts` | `100644` | 12977 | `90754501c25ee2b7af4720b00646b557eade48e9` | FETCHED / REVIEW IN PROGRESS | Timed mock scenarios, process spawn and cleanup registry. |
| `e2e/helpers/office.ts` | `100644` | 12777 | `1be26eea40612f011662129710b549d6a27f84c9` | FETCHED / REVIEW IN PROGRESS | Overlay, character, teammate and agent state assertions. |
| `e2e/helpers/standalone.ts` | `100644` | 15838 | `ab94998af9c08316a126ef1c2b0ca8f8c269cb17` | FETCHED / STATIC REVIEW IN PROGRESS | Standalone CLI spawn, URL capability handoff, port allocation, message recorder and process cleanup. |
| `e2e/helpers/team.ts` | `100644` | 6515 | `c5b2fc62b0cb0a37a8231245b615ac6a513d046d` | FETCHED / REVIEW IN PROGRESS | JSONL, team metadata and transcript builders. |
| `e2e/helpers/test-narration.ts` | `100644` | 7579 | `2299d56b0681c6b90180e62a417f07528299e627` | FETCHED / REVIEW IN PROGRESS | Cosmetic narration lifecycle and monitor terminal. |
| `e2e/helpers/webview.ts` | `100644` | 26416 | `5acec507ac0b5b28a8afcdf0528d1611b22d6386` | FETCHED / REVIEW IN PROGRESS | VS Code panel, frame, drawer and settings UI helpers. |
| `e2e/playwright.config.ts` | `100644` | 2291 | `4e4683186ee24e5cdce49a35cf336e069f35b1a2` | FETCHED / REVIEW IN PROGRESS | Playwright projects, retries, workers, reporters and output paths. |
| `e2e/run-config.ts` | `100644` | 550 | `1921baad5195765b91a6661afd2f75415c49be96` | FETCHED / REVIEW IN PROGRESS | Optional run-ID artifact path namespacing. |
| `e2e/tests/claude/hooks-off/areas-multiroot.spec.ts` | `100644` | 14271 | `2865f4de7e9d3cee9e21bd62d99bd008abc28160` | FETCHED / TEST STRUCTURE INVENTORIED | Multi-root area mapping and seat preference. |
| `e2e/tests/claude/hooks-off/areas.spec.ts` | `100644` | 4637 | `4b6b96fdee7634361bdc0b754c13038fb07bcf1d` | FETCHED / TEST STRUCTURE INVENTORIED | Single-root area data and editor gate. |
| `e2e/tests/claude/hooks-off/carpet.spec.ts` | `100644` | 13252 | `668eff0627a55adaf1682c3650c088321ba7fa81` | FETCHED / TEST STRUCTURE INVENTORIED | Carpet layer rendering, painting, erase, undo and persistence. |
| `e2e/tests/claude/hooks-off/editor.spec.ts` | `100644` | 30023 | `ce90cbb0399f9b2c1f954e60bdf581506bfd1b03` | FETCHED / TEST STRUCTURE INVENTORIED | Layout editor erase, stroke undo, desk groups and color operations. |
| `e2e/tests/claude/hooks-off/launch-drawer.spec.ts` | `100644` | 5073 | `d603c08951476a9dea6eba8cf0e69b1aeadcb14b` | FETCHED / TEST STRUCTURE INVENTORIED | VS Code multi-root and user Directory launch drawer. |
| `e2e/tests/claude/hooks-off/lifecycle.spec.ts` | `100644` | 38456 | `ad45fb01b44d6d7e9e47026aae87f6bf914e74f9` | FETCHED / TEST STRUCTURE INVENTORIED | Hooks-off lifecycle, polling and heuristic timer behavior. |
| `e2e/tests/claude/hooks-off/matrix.spec.ts` | `100644` | 15125 | `e85e7590259a9e663cac59f5645b559cc977421f` | FETCHED / TEST STRUCTURE INVENTORIED | Hooks-off internal/external × basic/inline/tmux spawn matrix. |
| `e2e/tests/claude/hooks-off/pets.spec.ts` | `100644` | 11352 | `37cc965cf3e73bea101553e68d325419704ff17e` | FETCHED / TEST STRUCTURE INVENTORIED | Pet asset loading, placement, persistence and click bubble. |
| `e2e/tests/claude/hooks-on/basic.spec.ts` | `100644` | 9175 | `741c72abca608de9bd621ef6c150ff19b81daf9c` | FETCHED / TEST STRUCTURE INVENTORIED | Hooks-on internal spawn, subagent and external adoption. |
| `e2e/tests/claude/hooks-on/consent.spec.ts` | `100644` | 28042 | `f2ffe2aa112c2b177aa05e523f0b94dfb24bfed2` | FETCHED / TEST STRUCTURE INVENTORIED | Hook consent, install/uninstall, migration and failure cases. |
| `e2e/tests/claude/hooks-on/lifecycle.spec.ts` | `100644` | 70687 | `1e89dc75cb4e9f30faeb8010ea6c2e339de9626c` | FETCHED / TEST STRUCTURE INVENTORIED | Hooks-on lifecycle, teams, sound, settings and editor regressions. |
| `e2e/tests/claude/hooks-on/teams.spec.ts` | `100644` | 24689 | `42937ceb5701034b1e313ae8947df16d8a4df79d` | FETCHED / TEST STRUCTURE INVENTORIED | Inline/tmux/new-harness teams and tool routing. |
| `e2e/tests/standalone/characters.spec.ts` | `100644` | 9847 | `df27b9c1dc16fbd9ab49642379f846f4bc90c879` | FETCHED / TEST STRUCTURE INVENTORIED | Character editing, wardrobe items and roster. |
| `e2e/tests/standalone/conversations.spec.ts` | `100644` | 10850 | `eee811c71bc214c6c7aeed9e1ce8bbb53c4e5f67` | FETCHED / TEST STRUCTURE INVENTORIED | Conversation history, resume, paging/search and Directory changes. |
| `e2e/tests/standalone/directory-modal.spec.ts` | `100644` | 4982 | `277893997d242ab44f372edcef2eaf9296c64cf9` | FETCHED / TEST STRUCTURE INVENTORIED | Directory modal keyboard and Area mapping. |
| `e2e/tests/standalone/hooks.spec.ts` | `100644` | 13707 | `c1067099831d32e8c5c4e35c8d6ae529c1775431` | FETCHED / TEST STRUCTURE INVENTORIED | Hook-driven lifecycle, consent and read-only spectator behavior. |
| `e2e/tests/standalone/launch-drawer.spec.ts` | `100644` | 20253 | `bdc56858997ab50e80cda4986d3955b0642a0bf5` | FETCHED / TEST STRUCTURE INVENTORIED | Directory launch, validation, suggestions, settings and viewport fit. |
| `e2e/tests/standalone/mobile.spec.ts` | `100644` | 29768 | `bc2cb73f0f7e63850bc2edd61a82ec2c6034245b` | FETCHED / TEST STRUCTURE INVENTORIED | Mobile shell, touch gestures, keyboard, clipboard and roster. |
| `e2e/tests/standalone/multi-server-hooks.spec.ts` | `100644` | 3417 | `adc90a44341487087609f58e2dbb502b65df5109` | FETCHED / TEST STRUCTURE INVENTORIED | Embedded VS Code and standalone hook-server isolation. |
| `e2e/tests/standalone/terminal.spec.ts` | `100644` | 17396 | `7d586c1045da35ae0b38662ef2183a3930ddda3c` | FETCHED / TEST STRUCTURE INVENTORIED | PTY terminal input, resize, URL copy/open and --no-terminal. |
| `e2e/tests/standalone/ui.spec.ts` | `100644` | 8747 | `0338c1494ce0199dbbbfe79f53caf4288feec0f3` | FETCHED / TEST STRUCTURE INVENTORIED | Close, debug view, assets, layout import/export and reconnect indicator. |
| `e2e/tsconfig.json` | `100644` | 494 | `5fc3efce347e5591b3021362d0b3365b65cc5114` | FETCHED / REVIEW IN PROGRESS | Strict E2E TypeScript compiler configuration. |

## Coverage totals

- 47 blobs across 7 directories.
- 21 spec files; 152 test declarations; 152 area tags.
- 8 platform skip guards, recorded separately from test declarations.
- No binary blob was present in the E2E subtree at this snapshot.
- No test was executed during GOV-E2E-001.

## Continuation

Complete assertion-level review for the 21 spec files and full static review for all harness/config files. Update each row only when its evidence level is actually reached.
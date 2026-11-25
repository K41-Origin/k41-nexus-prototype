# Testing Infrastructure

**Status:** STATIC INVENTORY COMPLETE — frameworks, commands, CI topology and documented test coverage mapped. Test execution and assertion adequacy remain OPEN.

**Source snapshot:** `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`.

## Test suites and commands

| Scope | Framework / config | Command | Inventory evidence | Execution status |
|---|---|---|---|---|
| Webview | Vitest 4; `webview-ui/vitest.config.ts` | `npm run test:webview` / `npm test -w webview-ui` | 54 test files in webview governance ledger | TEST-INVENTORIED; not executed in this audit |
| Server | Vitest 4; `server/vitest.config.ts` | `npm run test:server` / `npm test -w server` | 57 test/support files in server ledger; test files individually inventoried | TEST-INVENTORIED; not executed in this audit |
| Package contract | Node test runner | `npm run test:package-contract` | 7 declarations in `scripts/npm-package-contract.test.mjs` | TEST-INVENTORIED; not executed |
| E2E | Playwright; `e2e/playwright.config.ts` | `npm run e2e` | 21 spec files, 152 test declarations in E2E governance record | TEST-INVENTORIED; not executed |
| Protocol | AsyncAPI parser/generator checks | `npm run asyncapi:validate`, `npm run asyncapi:generate` plus drift check | CI step inventory | CI-INVENTORIED; not executed |
| Static quality | TypeScript, ESLint, Prettier, Knip | `npm run check-types`, `npm run lint`, `npm run format:check`, `npm run knip` | Root scripts and CI jobs | CI-INVENTORIED; not executed |
| Package smoke | Custom npm pack/install/CLI smoke | `npm run verify:npm-package` | Script statically reviewed; CI runs Node 20 smoke | TEST-INVENTORIED; not executed |

## Configuration behavior

- Webview Vitest includes `test/*.test.ts`, uses Node environment, 30-second timeout and disables file parallelism because tests share browser transport listeners and bind fixed Vite ports. Allure output is conditional on CI or `ALLURE`.
- Server Vitest includes `__tests__/**/*.test.ts`, has 10-second timeout, globals and an isolated HOME setup. Allure output is conditional on CI or `ALLURE`.
- Playwright uses `e2e/tests`, 120-second timeout, global setup, HTML and optional Allure reporters, test-level parallel distribution, one worker by default and one retry. The retry is explicitly intended to tolerate timing-sensitive behavior; it may reduce visible flake frequency and should be considered when interpreting a green run.
- CI E2E uses three operating systems (Ubuntu, macOS, Windows) × three shards, with one worker per shard. The separate package smoke job runs Node 20 while main/E2E jobs use the `.nvmrc` Node 22 selector.
- No dedicated `core/` test project is present. Core contracts/helpers are exercised indirectly by server/webview tests; exact function-to-assertion coverage is not exhaustively mapped.

## CI coverage flow

The CI main job installs with `npm ci`; validates AsyncAPI; regenerates and checks `core/src/messages.ts` drift; checks E2E inventory drift; runs typecheck, lint, webview tests, format check and advisory Knip; builds extension and webview; verifies package contract/artifact; runs server tests after build; and runs npm audit. A separate Node 20 job performs package contract and installed artifact smoke. E2E runs across the 3×3 matrix. `required-checks` aggregates the main CI, Node 20 package smoke and E2E matrix outcomes.

The CI workflow marks individual checks `continue-on-error: true` and then uses a final explicit fail gate to make the named blocking checks fail the job. Dependency audit and Knip are reported as advisory in the main job. The E2E job likewise has an explicit final failure gate.

## Test-quality evidence to preserve

- E2E-001–E2E-009 remain open; E2E-003–E2E-006 include assertion-quality gaps.
- Webview findings WV-TEST-01 and runtime/reconnect findings remain open.
- Server F-01–F-09 and adapter A-01–A-10 remain open/partially covered according to their owning records.
- Package contract suite is a separate Node test runner, not part of Vitest.
- No finding is closed by test inventory or CI configuration alone.

## Verification boundary

This document records configuration and test inventory, not test results. No test, build, typecheck, lint, Playwright, npm smoke or CI workflow was executed for this audit.

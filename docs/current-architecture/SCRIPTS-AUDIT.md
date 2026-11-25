# Scripts Deep Audit

**Status:** STATIC AUDIT COMPLETE — 14/14 tracked blobs individually fetched, SHA-reconciled and reviewed. Seven findings BUILD-001–BUILD-007 are OPEN.  
**Source branch / commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Documentation branch before publication:** `docs/hybrid-llm-engine-direction@ed01e33e6be1914509518d6fe56a8fe5f5b2944d`.  
**Inventory:** 14/14 script blobs; docs/source comparison 14/14 identical.  
**File ledger:** [SCRIPTS-FILE-REFERENCE.md](SCRIPTS-FILE-REFERENCE.md).

## 1. Governance and scope

Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, E2E module record, E2E DEEP-AUDIT and E2E FILE-REFERENCE before source review.

**Included:** every tracked blob directly beneath `scripts/`, including HTML utilities, generator/validator, E2E runner/inventory, report/Vercel tooling, npm contract/test/publication and npm/VSIX verification. `package.json` was fetched as integration context to trace script entry points.

**Excluded:** `.github/` workflow review, application-source re-audit, generated artifact regeneration, test/build/lint/typecheck/package/publish execution and runtime/browser/VS Code experiments. These are not implied by this static audit.

## 2. Complete tree and SHA reconciliation

The recursive source tree is complete (`truncated=false`). It contains one `scripts/` tree and exactly **14 blobs**. Each blob was individually fetched from main. A complete recursive tree at the current documentation branch commit was also fetched; comparison of all `scripts/` path/blob-SHA pairs found:
- source: 14 blobs;
- documentation branch: 14 blobs;
- added: 0; removed: 0; changed: 0.

The per-file modes, byte sizes, line counts and SHAs are in [SCRIPTS-FILE-REFERENCE.md](SCRIPTS-FILE-REFERENCE.md). No file was skipped.

## 3. Responsibilities and integration paths

### Generation and validation
- `generate-messages.ts` reads `core/asyncapi.yaml`, invokes Modelina, applies custom property/constant constraints, filters anonymous single-value aliases, writes `core/src/messages.ts`, then invokes Prettier and ESLint --fix. The generated TypeScript file remains derived output; the YAML is the declared source of truth.
- `validate-asyncapi.mjs` parses the same YAML and fails on error-severity diagnostics or a missing parsed document.
- `generate-e2e-inventory.mjs` invokes Playwright `--list --reporter=json`, groups specs by `@area:` tags, and replaces the marked inventory section in `e2e/README.md`.
- `run-e2e.mjs` forwards Playwright arguments and maps run-ID/video options into environment variables. Existing E2E-007 remains canonical for run-ID path namespace traversal; no duplicate finding was created.

### Reporting and deployment assembly
- `build-allure-report.mjs` collects `allure-results/{e2e,server,webview}`, filters per-suite metadata, rejects duplicate result filenames, writes GitHub/environment metadata, and invokes Allure generation into `allure-report/allure`. With no suite results it replaces the report directory with a placeholder.
- `assemble-vercel-output.mjs` deletes/recreates `.vercel/output`, copies the Allure report and writes redirects/config. It deploys only the report, not a live webview preview.

### Package and release
- `npm-package-contract.mjs` defines required/forbidden package paths, release identity, semver and tarball integrity checks.
- `verify-npm-package.mjs` packs the current project, validates the file list, installs the tarball into a temporary smoke project, checks CLI help/HTTP/SPA/assets/Hook ON setup, and cleans temporary files.
- `publish-npm-package.mjs` verifies tag/ref/repository/version and tarball integrity, then publishes only when the exact package version is absent; same integrity is a no-op and differing integrity is rejected.
- `verify-vsix-package.mjs` inventories required/forbidden archive paths, unpacks the VSIX and launches an extension-host smoke runner.
- The package contract test file contains seven unit-test declarations. They were inspected but not executed.

### Browser utilities
- `asset-manager.html` loads local JSON/PNG, edits asset/group metadata, keeps a localStorage snapshot, supports undo/redo and exports manifests/PNGs through the File System Access API.
- `jsonl-viewer.html` loads local JSONL files/folders, extracts conversations, models status transitions and renders parsed/raw/state views.
- `wall-tile-editor.html` loads a local wall sprite sheet and previews the 16 cardinal-neighbor bitmask variants.

## 4. Findings

### BUILD-001 — JSONL viewer renders untrusted tool names as HTML

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/jsonl-viewer.html:1140-1155` (tool names copied from parsed JSONL into event objects), `:1360-1362` (`evLabel` interpolates `e.toolName` without escaping), and `:1345-1349` (event label is inserted into `recordList.innerHTML`). Blob SHA: `b24142ac2afee30a250b6ec916a7332bf7163833`.
- **Trigger:** a user opens a JSONL file containing a crafted `message.content[].name` value for a `tool_use` block; the viewer generates a tool-start/subagent event and renders its label.
- **Technical explanation:** `evLabel()` returns a string containing the raw tool name. The returned string is interpolated into HTML in `renderRecordList()` and the state-event renderer. Other user-controlled text uses `esc()`, but this event label does not.
- **Impact:** crafted local logs can inject HTML/JavaScript into the viewer origin when inspected. Treat imported JSONL as untrusted input.
- **Remaining uncertainty:** no browser exploit was executed; finding is based on source dataflow and HTML sink.

### BUILD-002 — Asset Manager inserts editable group role into innerHTML without escaping

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/asset-manager.html:2370-2382` (free-form state role input), `:2510-2525` (role value is accepted/stored), and `:1975-1982` (role is concatenated directly into `group-info.innerHTML`). Blob SHA: `a21d7fcc5c69e9d091163410cb50c61ffdb5f541`.
- **Trigger:** a state-group role contains HTML markup, either entered in the free-form role input or loaded from a JSON project, and the corresponding asset/group membership panel is rendered.
- **Technical explanation:** the role is user-controlled but is not passed through `escHtml()` before HTML concatenation. Adjacent group name, ID, category and hierarchy values are escaped, making the missing role escaping a local inconsistency.
- **Impact:** crafted project metadata or role input can inject HTML/JavaScript into the asset-manager page.
- **Remaining uncertainty:** no browser exploit was executed.

### BUILD-003 — Asset Manager does not validate group graph cycles before recursive traversal

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/asset-manager.html:1767-1777` recursively follows `@group` references without a visited set or depth guard; `:660-680` accepts parsed JSON and immediately proceeds into rendering; `:2435-2443` only rejects direct self-membership during group creation. Blob SHA: `a21d7fcc5c69e9d091163410cb50c61ffdb5f541`.
- **Trigger:** an imported project contains cyclic group references (or duplicate group IDs create an invalid cyclic graph).
- **Technical explanation:** group definitions are not schema- or graph-validated when loaded. Recursive resolvers/renderers assume an acyclic hierarchy; the creation check catches only a group containing itself directly, not indirect cycles.
- **Impact:** loading, selecting or exporting a cyclic group hierarchy can recurse until stack exhaustion and make the editor unusable.
- **Remaining uncertainty:** no cyclic fixture was loaded and no runtime failure was reproduced.

### BUILD-004 — Discarded assets remain in grouped export output

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/asset-manager.html:3395-3417` collects all leaf assets of top-level groups without filtering `asset.discard`; `:3419-3435` explicitly filters discarded assets only in the ungrouped path. Blob SHA: `a21d7fcc5c69e9d091163410cb50c61ffdb5f541`.
- **Trigger:** a member of a group is marked discarded and the project is exported.
- **Technical explanation:** grouped members are resolved and passed into the manifest/PNG export regardless of their discard flag, while ungrouped assets are skipped when discarded.
- **Impact:** assets explicitly marked discarded can still be shipped in the generated furniture export, contradicting the approved/discarded distinction shown by the tool.
- **Remaining uncertainty:** no export was run; this is a direct branch-logic discrepancy.

### BUILD-005 — npm import-tileset command points to a missing script

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** TREE-VERIFIED / SOURCE-VERIFIED; command not executed
- **Evidence:** `package.json` declares `"import-tileset": "tsx scripts/import-tileset-cli.ts"`; the complete source tree at `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` contains no `scripts/import-tileset-cli.ts` or matching basename anywhere. Blob SHA for `package.json`: `66efe28fd2927466c5f8283c6e26fe63da427079`.
- **Trigger:** a developer runs `npm run import-tileset`.
- **Technical explanation:** the declared entry point is absent from the complete tracked tree. The command therefore cannot resolve the target module at this source snapshot.
- **Impact:** the declared npm script is unusable and provides a misleading repository entry point.
- **Remaining uncertainty:** no command was executed; absence is established by the complete tree.

### BUILD-006 — Allure failure path exits before staged-results cleanup

- **Severity:** Low
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/build-allure-report.mjs:181-199`, blob SHA `befd1d25bdf3b5d737e8f3ef269c7438cd3dc797`.
- **Trigger:** Allure generation returns a non-zero status.
- **Technical explanation:** the failure branch calls `process.exit()` from inside a `try` whose `finally` removes `stagedResultsDir`. `process.exit()` terminates immediately rather than unwinding the JavaScript stack, so the `finally` cleanup is bypassed.
- **Impact:** temporary staged Allure result directories can remain in the OS temp directory after a failed report build.
- **Remaining uncertainty:** no failed Allure generation was induced.

### BUILD-007 — npm smoke child is not awaited after SIGKILL

- **Severity:** Low
- **Confidence:** Medium
- **State:** OPEN
- **Verification:** STATIC-FINDING; not runtime reproduced
- **Evidence:** `scripts/verify-npm-package.mjs:87-95`, blob SHA `599b60cff9b10a22d06404d2b08e0c723baead15`.
- **Trigger:** the installed CLI smoke process does not close within the three-second SIGTERM wait.
- **Technical explanation:** the helper sends SIGKILL when `exitCode` is still null, then returns without awaiting the child's `close`/exit event. The caller's `finally` immediately removes the smoke root.
- **Impact:** cleanup does not establish that the child has exited before its temporary HOME/project files are removed. This is related to the process-teardown class already tracked by E2E-001, but applies to the npm package verifier.
- **Remaining uncertainty:** no stuck child or cleanup race was reproduced.

## 5. Existing findings reused; no duplicates created

- **E2E-001:** `verify-npm-package.mjs:87-95` has a related process termination/await boundary; BUILD-007 records the distinct package-verifier cleanup behavior.
- **E2E-002:** `verify-npm-package.mjs:46-66` also obtains a port by listening on port 0 and closing before the installed CLI binds. This is the same close-before-bind race pattern; it is cross-referenced rather than assigned a duplicate BUILD ID.
- **E2E-007:** run-ID namespace handling remains canonical in the E2E audit; no duplicate created.
- Existing server F-01–F-09, adapters A-01–A-10, webview findings and E2E-001–E2E-009 remain unchanged.

## 6. Verification boundaries

- **TREE-VERIFIED:** complete source tree, 14/14 `scripts/` blobs.
- **SHA-RECONCILED:** 14/14 path/blob-SHA pairs match the docs branch snapshot.
- **SOURCE-VERIFIED:** 14/14 files statically reviewed.
- **TEST-INVENTORIED:** seven declarations in `npm-package-contract.test.mjs`; assertions inspected.
- **TEST-EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.

No tests, builds, lint, typecheck, generation, package, publish, browser, VS Code host or runtime experiments were executed. No source, script, package manifest, dependency or configuration was modified.


## 7. BUILD-001–BUILD-007 remediation implementation snapshot

**Branch:** `security/remediate-build-008-009`  
**HEAD:** `4d29e1654b0d6a7bd3dacf3773e02265ab0069a1`  
**Base:** current `main` at `110c2c8c0907c710ef4a559c1dd647e6dee135c4`  
**Changed files:** `scripts/jsonl-viewer.html`, `scripts/asset-manager.html`, `scripts/build-allure-report.mjs`, `scripts/verify-npm-package.mjs`, `package.json`.

- **BUILD-001:** event labels now pass untrusted tool/subagent names through `esc()` before HTML rendering. SOURCE-VERIFIED; browser exploit test not executed.
- **BUILD-002:** group role now passes through `escHtml()` at the `innerHTML` sink. SOURCE-VERIFIED; browser exploit test not executed.
- **BUILD-003:** imported group graphs are validated before assignment; duplicate IDs and cycles throw an input error. Proposed group graphs are checked before commit; recursive resolution also carries an ancestor set. SOURCE-VERIFIED; cyclic fixture and editor interaction not executed.
- **BUILD-004:** grouped export leaf resolution filters `asset.discard`, matching ungrouped behavior. SOURCE-VERIFIED; export fixture not executed.
- **BUILD-005:** the broken `import-tileset` npm script was removed because the referenced file is absent and no CLI contract/implementation exists. This removes a dead command; it does not implement import functionality. Manifest parse and package smoke remain unexecuted.
- **BUILD-006:** Allure failure sets `process.exitCode` and allows `finally` to remove staged results. SOURCE-VERIFIED; forced failure/cleanup not executed.
- **BUILD-007:** child stop now awaits close after SIGTERM and SIGKILL and throws if the process never closes. SOURCE-VERIFIED; process-stop race not reproduced.

**Verification boundary:** all changed files re-fetched from the remediation branch; no tests, lint, format, typecheck, build, package, browser or runtime command executed. All seven findings remain OPEN pending executable regression verification.

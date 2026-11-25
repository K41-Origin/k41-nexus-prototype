# .github and CI/Release Deep Audit

**Status:** STATIC AUDIT COMPLETE — 10/10 tracked blobs individually fetched, SHA-reconciled and reviewed. BUILD-008–BUILD-013 are OPEN.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch/commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Docs branch before publication:** `docs/hybrid-llm-engine-direction@7b56bcd278311bfdb782acacdd80080f74d7e87a`.  
**Inventory:** 10/10 .github blobs; source/docs path+SHA comparison 10/10 identical.  
**File ledger:** [FILE-REFERENCE.md](FILE-REFERENCE.md).

## 1. Governance and scope

Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, scripts module record/deep audit/file reference and E2E module record before source inspection.

**Included:** every tracked blob beneath `.github/`; all four workflows reviewed in full; issue templates, PR template, Dependabot and funding metadata; targeted cross-reference to `package.json`, `scripts/npm-package-contract.mjs`, `scripts/publish-npm-package.mjs`, `scripts/verify-npm-package.mjs` and `scripts/verify-vsix-package.mjs`; read-only repository workflow/release/ruleset/branch metadata.

**Excluded:** workflow dispatch, actual CI execution, build/test/lint/typecheck, release/publish, secret-value inspection, marketplace/npm/Vercel account settings, organization settings not exposed through the connected GitHub API. No repository source or workflow was modified.

## 2. Complete inventory and repository state

The source recursive tree is complete (`truncated=false`), with 10 blobs beneath `.github/`. Every blob was individually fetched and reviewed. A complete recursive tree at the docs branch snapshot contains the same 10 paths and blob SHAs: 0 added, 0 removed, 0 changed. Per-file mode, bytes, lines and SHA are in [FILE-REFERENCE.md](FILE-REFERENCE.md).

Repository metadata observations:
- `main` still points to `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Branch metadata reports `protected: false` for `main`.
- Repository rulesets endpoint returned an empty list.
- The branch-protection detail endpoint returned 403 to the connected GitHub integration; organization-level rules were not independently verified.
- Actions workflow-runs API returned `total_count: 0`; releases API returned an empty list. These observations prevent validation against real execution or published release artifacts.
- Workflow and file content, not execution history, is the evidence basis for this static audit.

## 3. Trigger and workflow map

| Workflow | Trigger | Primary behavior |
|---|---|---|
| `ci.yml` | `pull_request` to main and `push` to main; ignores markdown, LICENSE and FUNDING-only changes | Protocol validation/generation drift, typecheck, lint, webview/server tests, package smoke, 3-OS × 3-shard E2E, dependency audit, Allure artifact upload, PR Vercel report preview |
| `pr-title.yml` | `pull_request_target`: opened, edited, synchronize | Semantic PR title check |
| `publish-extension.yml` | `release.published` and `workflow_dispatch` | VS Code Marketplace/Open VSX VSIX publication; npm trusted publication only on release event; GitHub release VSIX asset |
| `update-badges.yml` | schedule every 8 hours and `workflow_dispatch` | Reads marketplace/release/npm stats and writes three external Gist badges |
| `dependabot.yml` | weekly Monday npm and GitHub Actions update scans | One root npm workspace lockfile update; grouped minor/patch; Actions updates |

The issue templates request reproduction, environment and logs, and explicitly warn users to redact usernames/paths. The feature template asks users to check for duplicates. The PR template asks for test plan and E2E coverage. Dependabot correctly centralizes npm updates at root because the monorepo uses one root lockfile.

## 4. Permission and secret map

| Workflow/job | Declared permissions / secrets | Static assessment |
|---|---|---|
| CI default | Workflow-level `contents: read` | Least privilege for ordinary build/test jobs |
| CI deploy precheck | Inherits read; Vercel token/org/project IDs in job env | No checkout or project code execution in this job; it only downloads/merges artifacts and checks fork/secrets |
| CI deploy preview | `contents: read`, `issues: write`, `pull-requests: write`; Vercel secrets at job scope; checkout defaults to persisted credentials | **BUILD-008:** PR-controlled code executes in the same job with deployment secrets and a write-capable checkout token |
| PR title | `pull-requests: read`; built-in GITHUB_TOKEN | `pull_request_target` uses no checkout and only title metadata; safer separation, though action is tag-pinned rather than SHA-pinned |
| Extension publish | `contents: write`; `VSCE_PAT`, `OPEN_VSX_TOKEN`; checkout token is persisted by default | Release event has main-ancestry check; workflow_dispatch bypasses it and defaults to actual publication (BUILD-009) |
| npm publish | `contents: read`, `id-token: write`; npm OIDC trusted publishing | Publish step itself is release-event-only and validates release identity/integrity; OIDC permission is nevertheless granted to manual dispatch job executions too |
| Badge update | No explicit `permissions:` block; uses built-in GITHUB_TOKEN for public releases API and `GIST_SECRET`/`GIST_ID` for writes | Effective GITHUB_TOKEN rights depend on repository Actions settings; least privilege is not declared (BUILD-012) |

Secret names observed in workflow source: `VERCEL_TOKEN`, `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID`, `VSCE_PAT`, `OPEN_VSX_TOKEN`, `GIST_SECRET`, `GIST_ID`, and built-in `GITHUB_TOKEN`. No secret values, scopes, expiry, environment protection rules or organization secret policies were available to this audit.

## 5. Findings

### BUILD-008 — Same-repository PR code runs in Vercel-secret-bearing deployment job

- **Severity:** High
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** `.github/workflows/ci.yml:500-518` (precheck rejects fork PRs only); `:550-563` (deploy job permissions and Vercel secrets at job scope); `:566-585` (checkout followed by `npm ci`); `:608-626` (PR-controlled `npm run vercel:prepare` and Vercel CLI invocation). Workflow blob SHA: `79cbf4d0835327f67fec623b3b41651f41ed72f4`. `package.json:5-9, 70-90` defines project metadata/scripts; package manifest blob SHA: `66efe28fd2927466c5f8283c6e26fe63da427079`.
- **Trigger:** a same-repository pull request is eligible for preview deployment and changes package lifecycle scripts, dependencies or the `vercel:prepare` implementation. The precheck blocks fork PRs, but does not establish that same-repository PR code is trusted.
- **Technical explanation:** the deploy job exposes `VERCEL_TOKEN`, `VERCEL_ORG_ID` and `VERCEL_PROJECT_ID` to every step. It checks out the PR merge ref, runs `npm ci` (which can execute package lifecycle scripts), and then runs the PR-controlled `vercel:prepare` script before deployment. The checkout action does not set `persist-credentials: false`; its default credential persistence leaves the job's GITHUB_TOKEN in local Git configuration. That token inherits `issues: write` and `pull-requests: write` from the job permissions. PR-controlled code can therefore attempt to read/exfiltrate Vercel credentials and the persisted GitHub token.
- **Impact:** compromise of the Vercel deployment token and/or write-capable PR-comment token can enable unauthorized preview deployments, external disclosure or repository/PR comment manipulation. Exact blast radius depends on token scopes, which were not accessible.
- **Remaining uncertainty:** no malicious PR, package lifecycle script or token-exfiltration experiment was executed. Actual Vercel token scope and same-repository contributor policy are unknown.
- **Recommended direction:** run all PR-controlled install/build/report generation in an unprivileged job. Pass only a produced artifact into a separate minimal deploy job that does not checkout PR source or run package scripts. Scope the Vercel token to the deployment step only; set checkout `persist-credentials: false`; minimize/remove write permissions not needed by the deployment action.

### BUILD-009 — Manual publish dispatch can publish VSIX from a ref that is not on main

- **Severity:** High
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** `.github/workflows/publish-extension.yml:3-11` enables `workflow_dispatch` with `dry_run.default: false`; `:29-35` performs main-ancestry verification only when `github.event_name == 'release'`; `:65-81` runs Marketplace and Open VSX publishing steps without an event guard and maps the input directly to `dryRun`. Workflow blob SHA: `713dc93fbd4a8f5d2c5f37cebdbf37587615205d`.
- **Trigger:** a user with permission to dispatch the workflow selects a non-main branch/ref and leaves the default `dry_run=false`.
- **Technical explanation:** the main ancestry guard is conditional on release events. Manual dispatch skips it. The VSIX publish steps are unconditional and the default input means `dryRun: false`, so the VSIX produced from the selected checkout ref is published to both registries. The publish job also grants `contents: write` and uses checkout's default persisted credentials while executing `npm ci` and project scripts. The separate npm job grants `id-token: write` even for manual dispatch, although its final npm publish command is correctly guarded to release events.
- **Impact:** an accidental or malicious dispatch from a non-release ref can publish unreviewed extension code to the VS Code Marketplace and Open VSX. Code executed from that ref also runs in jobs with privileged token capabilities.
- **Remaining uncertainty:** no workflow dispatch was performed; the set of users allowed to dispatch and actual registry token scopes are not visible here.
- **Recommended direction:** enforce an explicit trusted ref/commit check for both release and manual dispatch before any privileged work. Make dry-run the safe default and require deliberate opt-in to publish. Avoid granting `contents: write` / `id-token: write` to manual dry-run paths unless needed.

### BUILD-010 — Required Checks aggregate is not shown as enforced by repository branch policy

- **Severity:** Medium
- **Confidence:** High for repository-level metadata; organization-level policy remains unverified
- **State:** OPEN
- **Verification:** TREE-VERIFIED / DOC-RECORDED (workflow) / repository metadata
- **Evidence:** `.github/workflows/ci.yml:426-437` explicitly describes `required-checks` as the aggregate status to require in branch protection. GitHub branch metadata for `main` reports `protected: false`; repository rulesets API returned an empty list. Source workflow blob SHA: `79cbf4d0835327f67fec623b3b41651f41ed72f4`.
- **Trigger:** a contributor pushes directly to main or merges without a repository/org policy requiring the aggregate status check.
- **Technical explanation:** the workflow creates a useful aggregate job, but the repository-level metadata does not show classic branch protection or repository rulesets enforcing it. CI running on push is post-commit and does not itself prevent an unverified commit from entering main.
- **Impact:** the intended required-check gate may be informational rather than an enforced merge/push control.
- **Remaining uncertainty:** branch-protection detail endpoint returned 403 to the connected integration; organization-level rulesets and hidden repository settings were not verified. Do not interpret this as proof that no organization policy exists.
- **Recommended direction:** verify organization rulesets and configure main to require the `Required Checks` status, PR review, and restricted direct pushes.

### BUILD-011 — GitHub Actions dependencies are referenced by mutable version tags, not immutable commit SHAs

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** examples include `.github/workflows/ci.yml:31,35,96,100,160,216,222,236,485,494,567,572,579,588,595,603`; `.github/workflows/pr-title.yml:15`; `.github/workflows/publish-extension.yml:25,39,53,66,75,85,98,112,140`; `.github/workflows/update-badges.yml:97,109,119`. Refs include `actions/checkout@v7`, `actions/setup-node@v7`, cache/upload/download actions, `amannn/action-semantic-pull-request@v6`, `HaaLeo/publish-vscode-extension@v2`, `softprops/action-gh-release@v3`, and `schneegans/dynamic-badges-action@v1.8.0`. Relevant workflow blob SHAs are listed in FILE-REFERENCE.md.
- **Trigger:** a referenced upstream action tag is moved or the upstream repository/tag is compromised.
- **Technical explanation:** version tags identify releases but are mutable Git refs; workflows do not pin third-party action code to full commit SHAs. Dependabot updates Actions weekly, which helps version freshness but does not make tag resolution immutable.
- **Impact:** workflow execution can consume changed third-party code without a corresponding reviewed workflow diff. Risk is amplified for actions receiving marketplace PATs, Gist secrets or write-capable GITHUB_TOKEN.
- **Remaining uncertainty:** no upstream action compromise or tag movement was observed.
- **Recommended direction:** pin each action to a verified full commit SHA and retain a version comment; use Dependabot to propose reviewed SHA updates.

### BUILD-012 — Badge workflow leaves GITHUB_TOKEN permissions to repository defaults

- **Severity:** Low
- **Confidence:** High that the workflow omits an explicit permission block; effective token rights unknown
- **State:** OPEN
- **Verification:** SOURCE-VERIFIED
- **Evidence:** `.github/workflows/update-badges.yml:8-12` declares the job and repository guard but no workflow/job `permissions:`; `:40-46` uses built-in `GITHUB_TOKEN` only to read public release metadata; `:96-128` passes the separate `GIST_SECRET` to the badge action. Blob SHA: `1c46d700a07cece5a31ff618ccc9804a95b78553`.
- **Trigger:** scheduled or manual badge workflow executes under a repository configuration whose default GITHUB_TOKEN permission is broader than read-only.
- **Technical explanation:** the workflow does not explicitly constrain the built-in token. Its release API call is read-only and the badge writes use a separate Gist credential, so broad GITHUB_TOKEN write access is not required by the visible workflow.
- **Impact:** effective token privilege can exceed the workflow's demonstrated need, increasing blast radius if the job/action is compromised.
- **Remaining uncertainty:** repository Actions default token permissions and the Gist token's actual scope were not accessible.
- **Recommended direction:** set explicit least-privilege permissions (likely `contents: read`, or no GITHUB_TOKEN permission if the public releases endpoint can be called anonymously) and keep Gist write scope isolated.

### BUILD-013 — VSIX publication does not validate release tag against package version

- **Severity:** Medium
- **Confidence:** High
- **State:** OPEN
- **Verification:** SOURCE-VERIFIED
- **Evidence:** `.github/workflows/publish-extension.yml:29-35` checks only that the release commit is an ancestor of main; `:51-87` packages, verifies and publishes the VSIX without checking tag/version equality. Workflow blob SHA: `713dc93fbd4a8f5d2c5f37cebdbf37587615205d`. `package.json:5` currently declares version `1.6.0`, blob SHA `66efe28fd2927466c5f8283c6e26fe63da427079`. By contrast, `scripts/npm-package-contract.mjs:130-136` requires release tag `v<manifest.version>`, blob SHA `329175a73e3bb2653dd7d2ada401492ecf3b5d4a`.
- **Trigger:** a GitHub Release is published with a tag that points to a main ancestor but does not equal `v<package.json.version>`.
- **Technical explanation:** the VSIX job checks ancestry only and can publish the manifest's extension version. The npm job independently enforces exact tag/ref/package version identity and can fail while the VSIX job succeeds because the jobs have no dependency between them.
- **Impact:** partial/inconsistent release: Marketplace/Open VSX can receive one version while npm publication rejects the same GitHub Release tag.
- **Remaining uncertainty:** no release exists in the API snapshot, so no historical mismatch was observed.
- **Recommended direction:** share a release-identity preflight across both publish jobs and require exact tag/ref/manifest version equality before either registry is touched.

## 6. CI gates and artifact provenance

### CI checks
- `ci.yml` runs on PRs targeting main and pushes to main; markdown/license/funding-only changes are ignored.
- The main CI job performs AsyncAPI validation/generation drift, E2E inventory drift, typecheck, lint, webview tests, formatting, build, npm package verification, server tests and dependency audit. Most checks use `continue-on-error: true`, then the final gate fails if any designated blocking step did not succeed. Knip and dependency audit are explicitly advisory in the summary.
- The E2E matrix covers Linux/macOS/Windows × three shards, uses OS-specific VS Code/Playwright caches and uploads Playwright/Allure artifacts.
- `required-checks` aggregates `ci`, `npm-package-node20` and the E2E matrix. The deploy-preview job is not part of this aggregate.

### Artifact flow and provenance
- **Allure:** per-suite and per-shard results are uploaded as GitHub Actions artifacts; the precheck downloads matching artifacts from the same workflow run, merges E2E results, re-uploads a merged artifact, and the preview job downloads it. Artifact names are explicit and run-scoped by the Actions artifact APIs. No independent signature/attestation or content digest comparison is implemented in workflow YAML.
- **npm:** `verify-npm-package.mjs` produces a tarball and `package-metadata.json` in the runner temp directory. The same job uploads that directory for retention (7 days) and then publishes from the local tarball path, not by downloading an arbitrary artifact. `publish-npm-package.mjs` checks package identity, release tag/ref/version, expected metadata ID, and SHA-512 tarball integrity against metadata before publishing. This is a meaningful integrity control. The metadata and tarball are generated in the same job; no separate signed attestation step is visible.
- **VSIX:** the package action creates one VSIX path; `verify-vsix-package.mjs` inspects required/forbidden archive paths and launches an extension-host smoke test; the same path is then passed to Marketplace, Open VSX and GitHub Release upload. This binds the published file to the locally verified file within the job, but the workflow does not create a checksum file or build-provenance attestation for the VSIX release asset.
- **Vercel preview:** generated from PR-controlled source and merged Allure artifacts. The secret-bearing job boundary is BUILD-008.

### Control-flow note
The deploy precheck exports `CI_RESULT` and `E2E_RESULT`, but its readiness decision checks only fork status and Vercel secret presence (`ci.yml:503-518`). Its skip-summary case branches for `ci-blocked` and `e2e-blocked` are not assigned by that decision (`:529-535`). The workflow comments call the Allure preview best-effort, so whether deployment should be blocked on failed tests is an unresolved design intent; do not silently assume either behavior.

## 7. Positive controls and existing findings reused

- CI workflow-level permission is `contents: read`; PR title workflow uses `pull-requests: read` and does not checkout PR source under `pull_request_target`.
- Fork PRs are explicitly excluded from Vercel preview secret use.
- The npm release script validates package name/repository/version, tag/ref identity, monotonic version and SHA-512 tarball integrity.
- VSIX is verified before the same output path is published to both registries.
- Dependabot updates root npm workspaces and GitHub Actions weekly.
- Existing BUILD-001–BUILD-007 remain canonical for scripts, HTML utilities, package helper behavior and E2E-related process/port patterns. Existing E2E-001–E2E-009, server F-01–F-09, adapters A-01–A-10 and webview findings remain unchanged.

## 8. Verification boundaries

- **TREE-VERIFIED:** complete recursive source tree; 10/10 .github blobs.
- **SHA-RECONCILED:** 10/10 source/docs path+blob-SHA pairs identical.
- **SOURCE-VERIFIED:** 10/10 .github blobs and named release integration files reviewed for the stated contracts.
- **DOC-RECORDED:** workflow descriptions and required-check intent.
- **TEST-EXECUTED:** none.
- **RUNTIME-REPRODUCED:** none.
- **API metadata:** 0 workflow runs and 0 releases returned; main branch reports `protected: false`; repository rulesets list empty. Branch-protection detail API was forbidden to the connected integration.

No workflow, test, build, lint, typecheck, release, publish, dispatch, deployment or runtime operation was executed. No application source, workflow, package manifest, dependency or configuration was modified.

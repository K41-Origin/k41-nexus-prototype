# .github and CI/Release Deep Audit

**Status:** STATIC AUDIT COMPLETE — 10/10 tracked blobs individually fetched, SHA-reconciled and reviewed. BUILD-008–BUILD-013 remain OPEN pending merge/runtime/settings verification; BUILD-008/009 remediation is implemented on `security/remediate-build-008-009`.  
**Repository:** `K41-Origin/k41-nexus-prototype`  
**Source branch/commit:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, 739 blobs, 117 trees, `truncated=false`.  
**Docs branch before publication:** `docs/hybrid-llm-engine-direction@7b56bcd278311bfdb782acacdd80080f74d7e87a`.  
**Inventory:** 10/10 .github blobs; source/docs path+SHA comparison 10/10 identical.  
**File ledger:** [FILE-REFERENCE.md](FILE-REFERENCE.md).

## 1. Governance and scope

Re-read governance README, AUDIT-REGISTER, AUDIT-METHOD, EVIDENCE-LOG, scripts module record/deep audit/file reference and E2E module record before source inspection.

**Included:** every tracked blob beneath `.github/`; all four workflows reviewed in full; issue templates, PR template, Dependabot and funding metadata; targeted cross-reference to `package.json`, `scripts/npm-package-contract.mjs`, `scripts/publish-npm-package.mjs`, `scripts/verify-npm-package.mjs` and `scripts/verify-vsix-package.mjs`; read-only repository workflow/release/ruleset/branch metadata.

**Excluded:** workflow dispatch, actual CI execution, build/test/lint/typecheck, release/publish, secret-value inspection, marketplace/npm/Vercel account settings, organization settings not exposed through the connected GitHub API. No repository source or workflow was modified during the original audit. A separate, explicitly authorized remediation branch is documented in §10.

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
| `ci.yml` | `pull_request` to main and `push` to main; ignores markdown, LICENSE and FUNDING-only changes | Protocol validation/generation drift, typecheck, lint, webview/server tests, package smoke, 3-OS × 3-shard E2E, dependency audit and Allure artifact upload; PR Vercel preview deployment removed in remediation branch |
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
- **Technical explanation:** the precheck rejects fork PRs but treats same-repository origin as sufficient for preview eligibility. GitHub's `pull_request` event checks out the PR merge ref by default, so repository content and package scripts executed by the deploy job are PR-controlled. The job exposes `VERCEL_TOKEN`, `VERCEL_ORG_ID` and `VERCEL_PROJECT_ID` at job scope, then runs `npm ci` and `npm run vercel:prepare`; the latter invokes PR-controlled report generation and scripts/assemble-vercel-output.mjs (package.json:121-125; assembler SHA 70391faa6cb171577bb02aff6e4a6e13963399ae). Arbitrary code reached through install lifecycle scripts or report preparation therefore executes in the same process trust domain as the Vercel credentials. The job also grants issues:write and pull-requests:write. Correction to GOV-GHCI-001: actions/checkout@v7 defaults persist-credentials to true, but its v7 implementation stores the token-bearing Git config in a separate file under RUNNER_TEMP and references it from repository Git config; the token value itself is not directly stored in .git/config. The credential file remains accessible to processes running as the same job identity, so the exposure conclusion is unchanged. External checkout v7 source: action.yml SHA 5b0524f730db83f9513c18ab31a6c086c7239076; src/git-auth-helper.ts SHA dd7e6fbdb5387908190676a6ec9c4a828a044dea, lines 326-358 and 369-429.
- **Impact:** compromise of the Vercel deployment token and/or write-capable PR-comment token can enable unauthorized preview deployments, external disclosure or repository/PR comment manipulation. Exact blast radius depends on token scopes, which were not accessible.
- **Remaining uncertainty:** no malicious PR, package lifecycle script or token-access experiment was executed. Actual Vercel token scope, repository Actions defaults, same-repository contributor policy and environment protections remain unknown. Runtime ACLs of the checkout credential file were not tested; source confirms its RUNNER_TEMP location and use for authenticated Git operations.
- **Recommended direction:** treat every PR as untrusted regardless of whether its head repository equals the base repository. Keep PR install/test/report generation in a job with no Vercel secret, no write-capable token and no OIDC permission. Move deployment into a trusted workflow sourced from the default branch; do not checkout or execute PR source there. Transfer only the exact artifact from the originating run, bind it to that run ID and digest, validate it as data-only, reject executable/function output, and regenerate or strictly validate Vercel config using trusted code. Run a pinned Vercel CLI against the validated prebuilt static output; expose Vercel credentials only to that deploy step. Put PR-comment updates in a separate least-privilege job. Artifact handoff alone is not a trust boundary unless the privileged consumer never executes artifact-controlled code or configuration.

### BUILD-009 — Manual publish dispatch can publish VSIX from a ref that is not on main

- **Severity:** High
- **Confidence:** High
- **State:** OPEN
- **Verification:** STATIC-FINDING
- **Evidence:** `.github/workflows/publish-extension.yml:3-11` enables `workflow_dispatch` with `dry_run.default: false`; `:29-35` performs main-ancestry verification only when `github.event_name == 'release'`; `:65-81` runs Marketplace and Open VSX publishing steps without an event guard and maps the input directly to `dryRun`. Workflow blob SHA: `713dc93fbd4a8f5d2c5f37cebdbf37587615205d`.
- **Trigger:** a user with permission to dispatch the workflow selects a non-main branch/ref and leaves the default `dry_run=false`.
- **Technical explanation:** GitHub permits manual dispatch to select a branch or tag; github.ref and GITHUB_SHA correspond to that selected ref. The main-ancestry guard is conditional on release events, so manual dispatch skips it. Both VSIX publish steps are unconditional and map inputs.dry_run directly to dryRun; the declared default is false. A dispatch from a non-main ref therefore packages that ref and publishes the resulting VSIX to Marketplace and Open VSX. Before publication, the same job runs npm ci, typecheck/lint, packaging and VSIX verification against the selected checkout with contents:write; checkout credential persistence is enabled by default. VSCE/Open VSX PATs are passed to the publish actions, not earlier install/build steps, but the write-capable GitHub token remains available through checkout's persisted credential file. Separately, publish-npm grants id-token:write for every event and runs npm installation, tests and package verification before its final release-only publish step. verify:npm-package invokes npm pack without --ignore-scripts by default; package.json declares prepack = npm run package (lines 95 and 103), and verify-npm-package.mjs invokes npm pack (lines 241-245). Thus selected-ref package/build scripts execute while id-token:write is available. GitHub documents that id-token:write permits any step/process in that job to request an OIDC JWT. npm trusted-publisher setup identifies the repository and workflow filename, with an optional environment; actual package configuration and branch/environment restrictions are unavailable. A manual-ref process can request an OIDC token, while npm acceptance of a direct publish from that identity remains conditional on the unverified publisher configuration.
- **Impact:** an accidental or malicious dispatch from a non-release ref can publish unreviewed extension code to the VS Code Marketplace and Open VSX. Code executed from that ref also runs in jobs with privileged token capabilities.
- **Remaining uncertainty:** no workflow dispatch or OIDC request was performed. Manual dispatch requires repository write access, but the authorized-user set, selected-ref controls, Marketplace/Open VSX token scopes, npm trusted-publisher configuration and GitHub Environment protections are not visible. The non-main VSIX publication path is statically established; npm OIDC acceptance from a manual ref is conditional, not reproduced or proven.
- **Recommended direction:** make manual dispatch a dry-run-only unprivileged path by default (dry_run: true) and do not grant it publisher credentials, contents:write or id-token:write. Prefer real publication only from a published release tag whose commit is an ancestor of main; if manual real publication is retained, require exact github.ref == refs/heads/main, a protected GitHub Environment with reviewer approval and allowed-branch restrictions, and immutable commit identity before privileged work. Split build/verification from publication: build the VSIX/npm tarball in an unprivileged job, then pass the same digest-verified artifact to isolated publisher jobs that do not run repository package scripts. Scope Marketplace/Open VSX secrets to their publish actions; scope npm OIDC permission to a trusted publisher job only, with no untrusted install/test/build steps before token use. Set checkout persist-credentials: false unless authenticated Git operations are required. Run a shared release-identity preflight before any registry is touched: release tag, github.ref, commit ancestry and package.json.version must agree; this also addresses BUILD-013.

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
- **Evidence:** examples include `.github/workflows/ci.yml:37,43,105,149,242,245,282,287,295,304,351,361,374,381,469,477,485,494,567,572,579,588,595,602`; `.github/workflows/pr-title.yml:15`; `.github/workflows/publish-extension.yml:25,39,53,66,75,85,98,112,140`; `.github/workflows/update-badges.yml:97,109,119`. Refs include `actions/checkout@v7`, `actions/setup-node@v7`, cache/upload/download actions, `amannn/action-semantic-pull-request@v6`, `HaaLeo/publish-vscode-extension@v2`, `softprops/action-gh-release@v3`, and `schneegans/dynamic-badges-action@v1.8.0`. Relevant workflow blob SHAs are listed in FILE-REFERENCE.md.
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

## 9. Trust-boundary design review — GOV-GHCI-002

**Design review status:** COMPLETE for the static source snapshot. BUILD-008 and BUILD-009 remain OPEN until controls are implemented and independently verified. This resolves the architecture/design assessment; it does not claim remediation.

### 9.1 Trust domains and assets

Treat PR merge refs, manually selected non-main refs, package manifests, lifecycle scripts, tests, report-generation scripts, generated Vercel output and build artifacts as untrusted inputs until an explicit trust policy promotes them.

Privileged capabilities present in the reviewed workflows:
- Vercel project credentials: VERCEL_TOKEN, VERCEL_ORG_ID, VERCEL_PROJECT_ID.
- GitHub job-token permissions, including issues:write and pull-requests:write in the preview job and contents:write in the extension publish job.
- Marketplace and Open VSX publisher credentials passed to the final publish actions.
- GitHub OIDC token-request capability granted by id-token:write in the npm job.

A safe boundary must ensure untrusted code never executes in a process/job that can read or request these capabilities. Same-repository origin is not a trust signal: it says where the ref lives, not whether its current contents are approved.

### 9.2 BUILD-008 — preview deployment boundary

**Source-verified execution chain:**
1. CI accepts pull_request events targeting main. GitHub documents that pull_request uses the PR merge ref and checkout defaults to the event ref.
2. The precheck at .github/workflows/ci.yml:500-518 rejects fork PRs only. It does not require a trusted author, reviewed commit, protected environment or explicit approval for same-repository PRs.
3. The deploy job at :550-585 has job-wide Vercel secrets, issues:write and pull-requests:write; it checks out the event ref and runs npm ci.
4. The step at :608-626 runs npm run vercel:prepare. The root manifest maps this to npm run test:report && node scripts/assemble-vercel-output.mjs (package.json:121-125). The report builder and assembler are repository-controlled code; the assembler copies Allure report files and writes .vercel/output/config.json (scripts/assemble-vercel-output.mjs:11-15,40-88).
5. The final step invokes vercel deploy --prebuilt with VERCEL_TOKEN. Vercel documents --prebuilt as uploading existing .vercel/output; this avoids a remote build but does not protect secrets already exposed to earlier local processes in the same job.
6. Checkout v7 source confirms default persist-credentials:true and a token-bearing Git config under RUNNER_TEMP, included from repository Git config. This corrects the narrower .git/config description in GOV-GHCI-001 while preserving the exposure conclusion: same-job code runs under the same runner identity and can access the credential file or use authenticated Git.

**Resolved design:** split PR report build and deployment into separate trust domains. PR CI must have read-only permissions and no deployment secret/OIDC. A trusted workflow/job sourced from the default branch consumes only the exact run-scoped artifact. It must not checkout the PR ref, run npm ci, execute artifact scripts, or trust artifact-supplied Vercel configuration. Validate the artifact as a constrained static-file set, regenerate trusted deployment metadata/configuration, pin deploy tooling, and inject Vercel credentials only into the deployment command. Keep comment-writing permissions in a separate job. If using workflow_run, defend against untrusted artifact content and bind download to the triggering run ID; the event itself is privileged and is not a substitute for artifact validation.

**Closure evidence required:** source proves no PR-controlled code or artifact configuration executes in a secret-bearing job; the privileged workflow is protected from PR modification; artifact identity/content validation is enforced; Vercel token scope and GitHub permissions are minimal; and an authorized test/review confirms the boundary. No such implementation or runtime evidence exists in this snapshot.

### 9.3 BUILD-009 — manual and release publication boundary

**Source-verified execution chain:**
1. workflow_dispatch exposes a branch dropdown and may target a branch or tag; GitHub documents that the workflow file must exist on the default branch, but the dispatched run ref/SHA are those of the selected ref.
2. The workflow declares dry_run.default:false (.github/workflows/publish-extension.yml:3-11). The main-ancestry check at :29-35 runs only for release; manual dispatch bypasses it.
3. The VSIX job checks out the event ref, grants contents:write, runs npm ci, typecheck/lint, packages and verifies a VSIX, then invokes both registry publish actions without an event guard (:15-81). Their dryRun input is the false-by-default manual input. Thus the non-main VSIX publication path is directly established.
4. VSCE/Open VSX secrets are only passed to their respective publish actions, but the earlier job still executes selected-ref code with a write-capable checkout credential. The checkout credential mechanism is the v7 temp-file mechanism described above, not a token literal stored directly in .git/config.
5. The npm job grants id-token:write at job scope and executes selected-ref package code before the release-only publish step (:88-154). The final step guard protects only the repository's intended npm publish script. GitHub OIDC documentation states id-token:write permits a job to request an ID token; npm trusted-publisher configuration requires repository and workflow filename and optionally an environment. Because actual package settings are unavailable, acceptance of a token minted during manual dispatch is INFERRED / OPEN, not proven. The direct VSIX publication issue does not depend on this uncertainty.

**Resolved design:** manual dispatch should be a dry-run-only, unprivileged workflow. Real publication should run from the immutable published release tag after a shared preflight proves tag/ref/commit ancestry/package version identity. If a manual real-publish path is retained, require exact refs/heads/main, protected Environment approval and branch restrictions before entering any privileged job. Build and test in a job without publisher secrets or OIDC; hand off a digest-verified artifact to isolated publisher jobs. Only trusted publisher jobs receive the relevant PAT or OIDC capability. OIDC must not be available while arbitrary install scripts, tests or package build code execute. Disable checkout credential persistence unless required. Reuse one release-identity preflight across VSIX and npm; keep BUILD-013 open until its tag/version mismatch path is fixed and verified.

**Closure evidence required:** manual non-main ref is rejected before any privileged execution; manual dry-run has no publisher secret, write token or OIDC capability; release tags are checked against main and manifest version before publication; the same verified artifact is published; protected Environment and npm trusted-publisher settings are verified; and authorized workflow tests confirm the gates. No dispatch, publish or OIDC request was performed in this audit.

### 9.4 External semantics and references

- GitHub pull_request merge-ref behavior: https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows#pull_request
- GitHub manual dispatch and selectable branch/ref: https://docs.github.com/en/actions/how-tos/manage-workflow-runs/manually-run-a-workflow
- GitHub OIDC permission and token-request interface: https://docs.github.com/en/actions/reference/security/oidc
- npm trusted publisher identity/configuration and automatic provenance: https://docs.npmjs.com/trusted-publishers/
- Vercel --prebuilt upload semantics: https://vercel.com/docs/cli/deploy
- actions/checkout v7 implementation: action.yml blob 5b0524f730db83f9513c18ab31a6c086c7239076; src/git-auth-helper.ts blob dd7e6fbdb5387908190676a6ec9c4a828a044dea.

### 9.5 Verification boundary

- TREE-VERIFIED: main recursive tree 856 entries, truncated=false; docs branch recursive tree 929 entries, truncated=false.
- SHA-RECONCILED: all 10 .github blobs match between source and documentation branch; no added, removed or changed blob.
- SOURCE-VERIFIED: both workflow files plus 8 targeted package/script integration files individually fetched at main snapshot.
- DOC-RECORDED: official GitHub, npm, Vercel and checkout-v7 semantics consulted for the trust-boundary design.
- STATIC-FINDING: BUILD-008 and BUILD-009 remain OPEN.
- TEST-EXECUTED / RUNTIME-REPRODUCED: none.
- Not verified: Vercel token scope, registry token scopes, npm trusted-publisher settings, Environment protection rules, authorized manual-dispatch users, org-level branch policies, actual workflow execution.

No workflow, test, build, lint, typecheck, publish, dispatch, deployment or runtime operation was executed. No application source, workflow, package manifest, dependency or configuration was modified.

## 10. BUILD-008/009 remediation implementation snapshot

**Remediation branch:** `security/remediate-build-008-009`  
**Branch HEAD at static verification:** `86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Base source:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Scope:** two workflow files only. No application source, package manifest, dependency or script changed.

### BUILD-008 — PR preview secret exposure removed by suspension

- `.github/workflows/ci.yml` no longer contains `deploy-preview-precheck` or `deploy-preview`; all Vercel secret references, write-capable PR permissions, PR checkout in the deploy job, PR-controlled `npm ci`/report preparation in a Vercel-credential-bearing job, and automated Vercel PR comments were removed from this workflow.
- Existing unprivileged CI, E2E and Allure artifact production remain. The PR-hosted Vercel report preview is intentionally **disabled**, not replaced by a privileged `workflow_run` deployer. This is a security-first mitigation; it trades preview convenience for removal of the secret-bearing PR execution path.
- The repository's Vercel secrets may still exist in GitHub settings; this workflow no longer references them. Secret scope, access history and possible historical exposure are not known.

### BUILD-009 — release-only publication with isolated publisher jobs

- `workflow_dispatch` now defaults `dry_run: true`; a manual dispatch with false is rejected. Publisher jobs are guarded to run only for `release.published`, so a manual ref can build and verify artifacts but cannot publish.
- `build-and-verify` has `contents: read`, checkout uses `persist-credentials: false`, and the job has no Marketplace/Open VSX secrets and no `id-token: write`. It verifies release commit ancestry, preserves npm release identity/version checks, runs package/test verification, and uploads one run-scoped artifact containing the VSIX, npm tarball and integrity metadata.
- `publish-vsix` consumes that artifact without checkout or repository scripts. Marketplace/Open VSX PATs are supplied only to their respective publish action steps; GitHub release upload receives GITHUB_TOKEN only in its own step.
- `publish-npm` consumes the same artifact without checkout or repository scripts. It alone has `id-token: write`, is assigned the `npm-publish` Environment, pins setup-node/download-artifact actions to resolved commit SHAs, installs npm 11.5.1 with lifecycle scripts disabled, verifies SHA-512 integrity and idempotent publication decision, then publishes the tarball with provenance.
- High-trust action refs used in publisher jobs were resolved from their upstream version tags and pinned to commits: setup-node `949feb2413d6458794dcd2491c4babbbce0c15c1`, download-artifact `9000827ccba6bdab643e8b6fd33ac0654aef8333`, HaaLeo publish extension `1b8df468b849f76a143b8c18bf0ffa2075398d12`, and softprops release `8a0cd651e5b2365bd640854a3fa5dfc70e4875f3`. This targeted pinning does not close repository-wide BUILD-011.
- BUILD-013 remains a separate finding; release identity preflight is retained in the unprivileged build job, but BUILD-013 is not reclassified or closed here.

### Static verification performed

- Branch tree retrieved completely: 856 entries, `truncated=false`.
- Only the two intended `.github/workflows/` blobs differ from the base snapshot in this remediation branch; new blob SHAs are recorded in FILE-REFERENCE.md.
- Both changed workflows were re-fetched from the remediation branch after the final workflow update.
- Static checks by inspection: no Vercel secret reference or deploy job remains in `ci.yml`; manual dispatch cannot satisfy publisher-job event guards; `id-token: write` exists only in the isolated npm publisher job; publisher jobs do not checkout repository source.
- No YAML parser, workflow linter, GitHub Actions run, tests, build, typecheck, lint, OIDC request, deployment, release or package publication was executed.

### Verification plan and closure gates

1. **Pre-merge static validation:** run a YAML/workflow parser and actionlint; verify expressions, job dependency graph, artifact paths, action outputs, shell quoting, npm CLI compatibility and permissions. Resolve all failures before merge.
2. **BUILD-008 PR negative test:** open a test PR and inspect the run's expanded jobs/permissions. Confirm no Vercel secret is injected, no deploy job exists, no write permission exists beyond the workflow's read-only default, and no Vercel API request/comment occurs. Confirm Allure CI artifacts remain available if required.
3. **BUILD-009 manual negative tests:** dispatch from a non-main branch with default dry_run=true and confirm build/verification only; dispatch with dry_run=false and confirm explicit rejection. Confirm neither publisher job starts, no VSCE/Open VSX secret is available, and no OIDC token can be requested by the build job.
4. **BUILD-009 release positive test in a controlled staging/release rehearsal:** create a release from a main ancestor with a matching package tag/version; verify build artifact upload, VSIX artifact digest/contents, npm SHA-512 metadata, publisher job isolation, Marketplace/Open VSX publication and npm provenance. Use staging credentials/packages first where supported; do not test against production registries without explicit release approval.
5. **GitHub settings gate:** configure and verify `npm-publish` Environment required reviewers and allowed deployment refs; verify npm trusted publisher exactly matches repository, workflow filename and Environment; verify VSCE_PAT/Open VSX token scope; confirm Actions default token permissions and organization rulesets. A workflow reference to an Environment does not itself configure its protection rules.
6. **Credential hygiene:** review workflow history and Vercel access logs for the former exposure window; rotate Vercel credentials if scope/history cannot rule out exposure. Remove unused Vercel secrets only after preview suspension is accepted.
7. **Post-merge verification:** retrieve merged workflow blobs, record exact run IDs/attempts, job permissions, artifact IDs/digests, registry outcomes and npm provenance URL. Close BUILD-008 only after the no-secret PR negative test; close BUILD-009 only after manual negative tests and release-path positive verification plus settings evidence.

**Current disposition:** remediation implemented on branch, not merged; BUILD-008 and BUILD-009 remain OPEN pending static parser validation, settings verification and authorized workflow evidence.

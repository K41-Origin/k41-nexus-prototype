# GitHub Workflows and CI/Release Audit Record

**Status:** STATIC AUDIT COMPLETE — 10/10 historical `.github/` blobs reviewed; BUILD-008–BUILD-014 source corrections are present on `security/remediate-build-008-009@4d29e1654b0d6a7bd3dacf3773e02265ab0069a1`; all remain OPEN pending verification/settings gates.  
**Source:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated=false`.  
**Documentation snapshot before publication:** `docs/hybrid-llm-engine-direction@7b56bcd278311bfdb782acacdd80080f74d7e87a`.  
**Detailed owner:** [GitHub CI/release deep audit](../../github-ci/DEEP-AUDIT.md).  
**File identity owner:** [GitHub file reference](../../github-ci/FILE-REFERENCE.md).

## Coverage
- 10/10 .github blobs individually fetched and reviewed; source/docs path+SHA comparison 10/10 identical.
- 4/4 workflows fully reviewed: CI, PR title, extension/npm publishing, badge update.
- 3 issue-template/config files, PR template, Dependabot and funding file reviewed.
- Targeted release integration trace covered package manifest, npm release identity/integrity helper and npm/VSIX verifier scripts.
- Workflow runs: API returned zero; releases: API returned empty; repository rulesets empty; main branch metadata `protected:false`. Organization-level enforcement and secret values/scopes remain unverified.

## Findings
Preserve BUILD-008–BUILD-014 in [DEEP-AUDIT.md](../../github-ci/DEEP-AUDIT.md):
- BUILD-008: historical finding was PR-controlled execution in a Vercel-secret-bearing preview job. Remediation branch removes the preview precheck/deploy jobs and all Vercel references from `ci.yml`; preview feature is suspended. OPEN pending PR negative-test evidence and merge.
- BUILD-009: remediation branch makes manual dispatch build/verify-only, release-gates publisher jobs, separates unprivileged artifact build from VSIX/npm publisher jobs, confines `id-token:write` to the isolated npm publisher, disables checkout credential persistence in the build job, and verifies artifact integrity/idempotency. OPEN pending negative/positive workflow tests and Environment/npm trusted-publisher settings.
- BUILD-010: required-check aggregate is not shown as enforced by repository branch policy; OPEN pending org-level policy verification.
- BUILD-011: all action references are now pinned to resolved full commit SHAs on the remediation branch; OPEN pending parser/actionlint and maintenance verification.
- BUILD-012: badge workflow now declares `contents: read`; OPEN pending workflow/settings verification.
- BUILD-013: shared release preflight now validates tag/ref/package version before both VSIX and npm publishing; OPEN pending negative/positive release tests.
- BUILD-014: three protocol/inventory checks are now blocking and included in the final gate; OPEN pending YAML/actionlint and an actual CI run.
- Existing BUILD-001–BUILD-007 remain in the scripts audit; no IDs reused.

## Next action
Complete BUILD-010 repository branch-protection/ruleset configuration with an authorized admin; run the workflow/test/release verification gates for BUILD-008–BUILD-014; merge only after evidence is recorded in [DEEP-AUDIT.md §10](../../github-ci/DEEP-AUDIT.md). BUILD-008 preview deployment is intentionally suspended. Before closure, verify PR/manual negative tests, release positive rehearsal, organization rulesets, Actions defaults, Vercel/registry secret scopes, `npm-publish` Environment protection, npm trusted publisher and artifact provenance. No workflow was dispatched in the remediation session.


## Trust-boundary design review — GOV-GHCI-002

- BUILD-008 design review is complete: same-repository PR code is untrusted. Required boundary: unprivileged PR build, exact run-scoped data-only artifact, trusted deploy workflow that never checks out or executes PR code, validated static output/config, Vercel secret only in the deploy step, and separate comment permissions.
- BUILD-009 design review is complete: manual dispatch can select a branch/tag and the false-by-default dry-run allows non-main VSIX publication. The latest static evidence also traces `verify:npm-package` → `npm pack` → package `prepack` (`npm run package`) while the job has `id-token:write`; actual OIDC token request or npm acceptance was not tested. Required boundary: manual dry-run without privilege; real publication only from a validated release tag (or protected main-only manual route); shared tag/ref/commit/version preflight; isolated publisher jobs; verified artifact; OIDC only in the trusted npm publisher job.
- Checkout credential correction: actions/checkout@v7 stores the token-bearing Git config in a separate RUNNER_TEMP file referenced from repository Git config. The token value itself is not directly stored in .git/config; default persistence still makes the credential available to same-job processes.
- Design assessment complete is not remediation: BUILD-008/009 remain OPEN because no workflow/source changes or runtime verification were authorized. See [DEEP-AUDIT.md §9](../../github-ci/DEEP-AUDIT.md) and [FILE-REFERENCE.md](../../github-ci/FILE-REFERENCE.md).



## Remediation branch snapshot

- Branch: `security/remediate-build-008-009`
- HEAD: `86d3b1dbddae7605e5e2092886c12982f8e4bf58`
- Base: `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`
- `ci.yml`: `8dc8cdf30e2cb1857708a8bd601807c08df34368` — privileged PR preview deployment removed.
- `publish-extension.yml`: `0b408f04d6b7c47d1405b9259d257fd21bdcb15d` — release-only isolated publishers; manual verification-only.
- No application source, package manifest, dependency or package script changed.
- Remediation is not closure: both findings remain OPEN until branch merge and their respective verification gates pass.

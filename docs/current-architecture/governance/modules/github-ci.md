# GitHub Workflows and CI/Release Audit Record

**Status:** STATIC AUDIT + TRUST-BOUNDARY DESIGN REVIEW COMPLETE — 10/10 `.github/` blobs and 8 targeted integration files SOURCE-VERIFIED; BUILD-008–BUILD-013 remain OPEN pending implementation/settings evidence.  
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
Preserve BUILD-008–BUILD-013 in [DEEP-AUDIT.md](../../github-ci/DEEP-AUDIT.md):
- BUILD-008: same-repository PR-controlled code executes in Vercel-secret-bearing preview deployment job; OPEN.
- BUILD-009: manual workflow dispatch can publish VSIX from a ref not verified as main; additionally, `npm pack` can invoke package `prepack` scripts while `id-token:write` is available in the npm job. The added npm OIDC execution edge is part of BUILD-009, not a new finding; OPEN.
- BUILD-010: required-check aggregate is not shown as enforced by repository branch policy; OPEN pending org-level policy verification.
- BUILD-011: GitHub Actions dependencies use mutable tags rather than full commit SHA pins; OPEN.
- BUILD-012: badge workflow omits explicit GITHUB_TOKEN permissions; OPEN.
- BUILD-013: VSIX release path does not enforce tag/package-version identity; OPEN.
- Existing BUILD-001–BUILD-007 remain in the scripts audit; no IDs reused.

## Next action
Implement the GOV-GHCI-002 design only after explicit authorization. Before closure, verify organization rulesets, repository Actions defaults, Vercel/registry secret scopes, protected Environment rules, npm trusted-publisher configuration and artifact provenance; then run authorized workflow verification. Runtime execution remains unauthorized.


## Trust-boundary design review — GOV-GHCI-002

- BUILD-008 design review is complete: same-repository PR code is untrusted. Required boundary: unprivileged PR build, exact run-scoped data-only artifact, trusted deploy workflow that never checks out or executes PR code, validated static output/config, Vercel secret only in the deploy step, and separate comment permissions.
- BUILD-009 design review is complete: manual dispatch can select a branch/tag and the false-by-default dry-run allows non-main VSIX publication. The latest static evidence also traces `verify:npm-package` → `npm pack` → package `prepack` (`npm run package`) while the job has `id-token:write`; actual OIDC token request or npm acceptance was not tested. Required boundary: manual dry-run without privilege; real publication only from a validated release tag (or protected main-only manual route); shared tag/ref/commit/version preflight; isolated publisher jobs; verified artifact; OIDC only in the trusted npm publisher job.
- Checkout credential correction: actions/checkout@v7 stores the token-bearing Git config in a separate RUNNER_TEMP file referenced from repository Git config. The token value itself is not directly stored in .git/config; default persistence still makes the credential available to same-job processes.
- Design assessment complete is not remediation: BUILD-008/009 remain OPEN because no workflow/source changes or runtime verification were authorized. See [DEEP-AUDIT.md §9](../../github-ci/DEEP-AUDIT.md) and [FILE-REFERENCE.md](../../github-ci/FILE-REFERENCE.md).


# GitHub Workflows and CI/Release Audit Record

**Status:** STATIC AUDIT COMPLETE — 10/10 tracked `.github/` blobs fetched, SHA-reconciled and reviewed; BUILD-008–BUILD-013 OPEN.  
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
- BUILD-009: manual workflow dispatch can publish VSIX from a ref not verified as main; OPEN.
- BUILD-010: required-check aggregate is not shown as enforced by repository branch policy; OPEN pending org-level policy verification.
- BUILD-011: GitHub Actions dependencies use mutable tags rather than full commit SHA pins; OPEN.
- BUILD-012: badge workflow omits explicit GITHUB_TOKEN permissions; OPEN.
- BUILD-013: VSIX release path does not enforce tag/package-version identity; OPEN.
- Existing BUILD-001–BUILD-007 remain in the scripts audit; no IDs reused.

## Next action
Resolve the privileged preview and manual publish trust boundaries first. Verify actual organization rulesets, repository Actions default token permissions, secret scopes/environment protections, trusted npm publisher settings and release artifact attestations when access is available. Runtime execution remains unauthorized.

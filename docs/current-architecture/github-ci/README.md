# GitHub Workflows and CI/Release Audit

**Status:** STATIC AUDIT COMPLETE — 10/10 historical `.github/` blobs and 8 targeted integration files reviewed. BUILD-008/009 remediation is implemented on `security/remediate-build-008-009` (not merged; OPEN pending verification); BUILD-010–BUILD-013 remain OPEN.  
**Source:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, complete (`truncated=false`).  
**Detailed audit:** [DEEP-AUDIT.md](DEEP-AUDIT.md).  
**File ledger:** [FILE-REFERENCE.md](FILE-REFERENCE.md).

This scope covers every tracked blob under `.github/`, workflow triggers/permissions/secrets, CI gates, artifact flow, release publication and available repository enforcement metadata. Cross-references existing scripts findings BUILD-001–BUILD-007; it does not repeat their source review.

Static/read-only only. No workflow dispatch, test, build, release, publish or runtime action was executed. Secret values and repository/org settings not exposed by the GitHub API are not claimed as inspected.


## BUILD-008/009 remediation branch

The separate branch `security/remediate-build-008-009` removes the privileged PR preview deployment path (preview intentionally suspended) and changes release publishing to a manual verification-only path plus isolated release-only publisher jobs. See [DEEP-AUDIT.md §10](DEEP-AUDIT.md) for exact blob identities, scope, verification gates and outstanding settings dependencies.

No workflow execution, tests, build, lint, typecheck, OIDC request or publication was performed. Do not mark either finding closed until the listed negative/positive workflow tests and GitHub/npm settings checks are recorded.

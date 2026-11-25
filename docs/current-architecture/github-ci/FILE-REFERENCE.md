# .github File Reference

**Status:** COMPLETE — 10/10 blobs individually fetched and reviewed.  
**Source:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`.  
**Documentation snapshot before publication:** `docs/hybrid-llm-engine-direction@7b56bcd278311bfdb782acacdd80080f74d7e87a`.  
**Comparison:** source/docs branch .github path+blob-SHA pairs 10/10 identical; 0 added, 0 removed, 0 changed.

| Path | Mode | Bytes | Lines | Blob SHA | Review |
|---|---:|---:|---:|---|---|
| `.github/FUNDING.yml` | 100644 | 43 | 3 | `4633651078f09de9adab763e1e88a14d89a8df70` | SOURCE-VERIFIED |
| `.github/ISSUE_TEMPLATE/bug_report.yml` | 100644 | 2,599 | 85 | `f8de707d3148b5b2dbcf8480e9e7ddda776e4dfd` | SOURCE-VERIFIED |
| `.github/ISSUE_TEMPLATE/config.yml` | 100644 | 213 | 6 | `9a83ca0cd2a69a4ac8eacfdede3446948e4f2140` | SOURCE-VERIFIED |
| `.github/ISSUE_TEMPLATE/feature_request.yml` | 100644 | 1,652 | 54 | `231488cd4ca2df7bbc466755dd3206f11c95975b` | SOURCE-VERIFIED |
| `.github/dependabot.yml` | 100644 | 814 | 27 | `58ddf528590ac1c90104f05e5641459feaa53827` | SOURCE-VERIFIED |
| `.github/pull_request_template.md` | 100644 | 1,049 | 45 | `e380dd4e0d876c1f7ea43303e2ae2e61f6c3cdcf` | SOURCE-VERIFIED |
| `.github/workflows/ci.yml` | 100644 | 24,588 | 683 | `79cbf4d0835327f67fec623b3b41651f41ed72f4` | SOURCE-VERIFIED |
| `.github/workflows/pr-title.yml` | 100644 | 712 | 33 | `ee43fc0a6a2c6beb914756a0b8877a24abcd75d3` | SOURCE-VERIFIED |
| `.github/workflows/publish-extension.yml` | 100644 | 4,850 | 154 | `713dc93fbd4a8f5d2c5f37cebdbf37587615205d` | SOURCE-VERIFIED |
| `.github/workflows/update-badges.yml` | 100644 | 5,041 | 129 | `1c46d700a07cece5a31ff618ccc9804a95b78553` | SOURCE-VERIFIED |

## Coverage

- Complete recursive source tree: `.github/` has 10 blobs across workflows, templates, Dependabot and funding metadata.
- 10/10 individually fetched from main.
- 10/10 path/blob-SHA pairs match the docs branch tree.
- 10/10 source-reviewed; no omitted or unverified file.
- Workflows: 4/4 reviewed in full, including all 683 lines of CI.
- Workflow executions, releases, tags and actual secret values were not executed/read beyond available API metadata; see DEEP-AUDIT.md limitations.


## Targeted trust-boundary integration files

**Verification snapshot:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`, `truncated=false`. These files were fetched individually for GOV-GHCI-002; they are cross-boundary dependencies, not additional `.github/` inventory entries.

| Path | Mode | Bytes | Lines | Blob SHA | Review scope |
|---|---:|---:|---:|---|---|
| `package.json` | 100644 | 6,020 | 182 | `66efe28fd2927466c5f8283c6e26fe63da427079` | Root lifecycle/script entry points: `prepare`, `vercel:prepare`, tests and package verification |
| `server/package.json` | 100644 | 479 | 23 | `f3e7e63e1920d6804eec70c5104914c436269351` | Workspace lifecycle/script surface |
| `webview-ui/package.json` | 100644 | 1,113 | 44 | `3729872b2736eaf6b66c608c1b40f4e24c25d2fb` | Workspace lifecycle/script surface |
| `scripts/assemble-vercel-output.mjs` | 100644 | 2,672 | 89 | `70391faa6cb171577bb02aff6e4a6e13963399ae` | Static Vercel output and config generation |
| `scripts/npm-package-contract.mjs` | 100644 | 5,585 | 165 | `329175a73e3bb2653dd7d2ada401492ecf3b5d4a` | Release tag/ref/version and tarball-integrity contract |
| `scripts/publish-npm-package.mjs` | 100644 | 3,699 | 114 | `7cfcf7bb2d64aa62dd5ed70f7de9a212af4bff82` | Guarded npm publication path |
| `scripts/verify-npm-package.mjs` | 100644 | 10,867 | 296 | `599b60cff9b10a22d06404d2b08e0c723baead15` | Package build/pack/smoke verification execution path |
| `scripts/verify-vsix-package.mjs` | 100644 | 4,804 | 134 | `ed4d2e5476cfc6c7d3a9ec5865ecc3ee1e4f4803` | VSIX inventory and extension-host verification |

All 8 integration files were individually fetched and statically reviewed for the stated scope. No test or script was executed.

**Metadata correction:** the recursive Git tree reports `.github/workflows/ci.yml` as 24,588 bytes. The earlier 24,566 value in this ledger was inaccurate; the blob SHA and 683-line review identity are unchanged.


## Remediation branch verification snapshot

**Branch:** `security/remediate-build-008-009`  
**HEAD:** `86d3b1dbddae7605e5e2092886c12982f8e4bf58`  
**Base:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`  
**Tree:** 856 entries, `truncated=false`. Only these two tracked workflow blobs differ from the base tree within the remediation change set.

| Path | Mode | Bytes | Lines | Blob SHA | Remediation review |
|---|---:|---:|---:|---|---|
| `.github/workflows/ci.yml` | 100644 | 16,167 | 451 | `8dc8cdf30e2cb1857708a8bd601807c08df34368` | TREE-VERIFIED / SOURCE-VERIFIED — privileged preview jobs removed |
| `.github/workflows/publish-extension.yml` | 100644 | 6,627 | 238 | `0b408f04d6b7c47d1405b9259d257fd21bdcb15d` | TREE-VERIFIED / SOURCE-VERIFIED — release-only isolated publisher design |

These are branch-specific remediation identities, not replacements for the historical `main@56f354f` audit baseline above. The workflow files were fetched after the final branch update. No workflow parser, actionlint, tests, build, lint, typecheck or runtime execution was performed.

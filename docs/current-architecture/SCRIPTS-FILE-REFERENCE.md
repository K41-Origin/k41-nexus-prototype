# Scripts File Reference

**Status:** COMPLETE — 14/14 tracked blobs individually fetched and source-reviewed at `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.  
**Source tree:** `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50` — 856 entries, `truncated=false`.  
**Documentation snapshot:** `docs/hybrid-llm-engine-direction@ed01e33e6be1914509518d6fe56a8fe5f5b2944d`; 14/14 script path/blob-SHA pairs match, 0 added, 0 removed, 0 changed.  
**Detailed findings:** [SCRIPTS-AUDIT.md](SCRIPTS-AUDIT.md).

## Complete inventory

All paths below are `TREE-VERIFIED`, `SHA-RECONCILED`, individually `FETCHED` and `SOURCE-VERIFIED` for static review. HTML tools were reviewed across their complete source; executable scripts were reviewed through EOF.

| Path | Mode | Bytes | Lines | Blob SHA | Responsibility |
|---|---:|---:|---:|---|---|
| `scripts/assemble-vercel-output.mjs` | 100644 | 2,672 | 89 | `70391faa6cb171577bb02aff6e4a6e13963399ae` | Assemble Vercel static output from the Allure report. |
| `scripts/asset-manager.html` | 100644 | 152,254 | 3,667 | `a21d7fcc5c69e9d091163410cb50c61ffdb5f541` | Browser-based tileset metadata, group editing and PNG export utility. |
| `scripts/build-allure-report.mjs` | 100644 | 5,220 | 202 | `befd1d25bdf3b5d737e8f3ef269c7438cd3dc797` | Merge suite results and generate the Allure report. |
| `scripts/generate-e2e-inventory.mjs` | 100755 | 5,466 | 153 | `1e063c590c71b742d58b41e3bc41a92deccf6478` | Generate the E2E README inventory from Playwright JSON listing. |
| `scripts/generate-messages.ts` | 100644 | 7,065 | 164 | `19961b5a07cf1cd8c7d9ba1d55a0fddddbcadbec` | Generate `core/src/messages.ts` from AsyncAPI source. |
| `scripts/jsonl-viewer.html` | 100644 | 71,911 | 1,565 | `b24142ac2afee30a250b6ec916a7332bf7163833` | Local JSONL session/conversation and state viewer. |
| `scripts/npm-package-contract.mjs` | 100644 | 5,585 | 165 | `329175a73e3bb2653dd7d2ada401492ecf3b5d4a` | npm artifact, release identity and integrity contract. |
| `scripts/npm-package-contract.test.mjs` | 100644 | 4,129 | 125 | `3ac82e5eabeab79799ca251f18829628198ff60f` | Unit tests for npm package contract helpers. |
| `scripts/publish-npm-package.mjs` | 100644 | 3,699 | 114 | `7cfcf7bb2d64aa62dd5ed70f7de9a212af4bff82` | Guarded npm publication and idempotency decision. |
| `scripts/run-e2e.mjs` | 100644 | 1,508 | 58 | `2aa5b1bbafcc458dae06e4029b40153116188cac` | Playwright CLI wrapper and E2E run options. |
| `scripts/validate-asyncapi.mjs` | 100644 | 2,011 | 59 | `75f20b232d91fb6d8ba8063e67c2a805fcd9a778` | Validate `core/asyncapi.yaml` using AsyncAPI Parser. |
| `scripts/verify-npm-package.mjs` | 100644 | 10,863 | 296 | `599b60cff9b10a22d06404d2b08e0c723baead15` | Pack, inspect and smoke-test the npm tarball. |
| `scripts/verify-vsix-package.mjs` | 100644 | 4,804 | 134 | `ed4d2e5476cfc6c7d3a9ec5865ecc3ee1e4f4803` | Inspect VSIX contents and run extension-host smoke verification. |
| `scripts/wall-tile-editor.html` | 100644 | 11,180 | 324 | `8113d86442e5ed9005f1779bdcc555c177b6432c` | Interactive wall autotile mask preview/editor. |

## Test inventory
- `scripts/npm-package-contract.test.mjs`: 7 `node:test` declarations; assertion-level source review complete (`TEST-INVENTORIED`).
- `TEST-EXECUTED`: none.
- No other test files are tracked directly under `scripts/`.

## Coverage
- 14/14 blobs in the complete tree.
- 14/14 individually fetched.
- 14/14 SHA-reconciled against the documentation branch.
- 14/14 source-reviewed.
- No unverified file remains in `scripts/`.

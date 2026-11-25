# Audit Method and Evidence Rules

## 1. Audit session contract

Every audit session must record:

- repository and branch;
- exact commit SHA (and baseline commit if comparing);
- scope and explicit exclusions;
- paths inspected and each blob SHA;
- line ranges for large files;
- commands executed, exit codes and relevant output;
- findings created, updated, rejected or closed;
- unresolved questions and the exact next continuation point.

Do not claim that a repository, module, file, test suite, or runtime was inspected beyond the evidence recorded.

## 2. SHA-first no-duplication workflow

1. Read the module record and audit register before touching source.
2. Fetch the current recursive Git tree for the selected commit; require `truncated: false` before treating counts as complete.
3. Compare each path and blob SHA against its file ledger.
4. For unchanged files:
   - `REVIEWED`: do not repeat the same static review;
   - `REVIEWED REPORTED`: preserve prior work, but close the specifically recorded line-coverage/cross-reference gap;
   - `INVENTORIED`: inspect source; inventory is not review;
   - `PARTIAL`: inspect only missing ranges/questions;
   - `NOT VERIFIED`: obtain the missing evidence or preserve as open with reason;
   - binary: record the exact validation performed (signature/header/metadata/full decode), not a generic “reviewed”.
5. For changed files, inspect the diff and the changed context. Revisit dependent files only where behavior/contracts may be affected.
6. Record new SHA, review scope and result in the owning ledger.

A file's path is not its identity. Its blob SHA at the audit commit is. A branch movement does not automatically mean every file changed.

### Required reconciliation gate before every continuation

- Record the active source branch + commit and the documentation branch + commit separately. Never treat a docs-branch commit as an application-source baseline.
- Retrieve the complete recursive source tree (`truncated: false`) for the active source commit and compare `(path, blob SHA)` pairs against the owning module ledger. If the tree is truncated, inventory reconciliation is OPEN.
- Preserve historical module baselines as provenance. Add a new verification snapshot when a newer source commit is checked; do not overwrite the historical baseline or imply that a branch-tip movement changed source content.
- Classify each ledger row before reading source. Re-fetch/review only changed blobs, explicit `INVENTORIED`/`PARTIAL`/`NOT VERIFIED` gaps, or unchanged files required for a named cross-module trace. Do not repeat an unchanged `REVIEWED` file for the same question.
- Keep source review and test review separate. `TEST-INVENTORIED` means assertions were inspected; `FETCHED / INVENTORIED` means the test file was fetched/catalogued but assertion-level review is still open; neither means executed.
- Before creating a finding, search the owning deep-audit record for an existing ID/behavior. Extend an existing finding when the behavior is the same; create a new ID only for a materially distinct behavior. Never recycle closed/rejected IDs.
- Update the owning module ledger/record first, then register summary, then append the evidence-log session. The register must point to the actual next gap, not stale historical continuation text.

## 3. Evidence levels

Use explicit labels:

- **SOURCE-VERIFIED:** directly observed in the exact source revision; include path and line range.
- **DOC-RECORDED:** present in a document; not independently established by source unless cross-checked.
- **TREE-VERIFIED:** established from a complete Git tree (path, mode, blob SHA, size).
- **STATIC-FINDING:** code path indicates a defect/risk; not runtime reproduced.
- **TEST-INVENTORIED:** test file/assertions inspected; tests were not executed.
- **TEST-EXECUTED:** exact command ran; record environment, exit code and result.
- **RUNTIME-REPRODUCED:** behavior reproduced in a named host/environment with steps and observed output.
- **INFERRED:** supported by evidence but not directly established; state the inference.
- **OPEN:** evidence required to decide.

Do not promote INFERRED or STATIC-FINDING to RUNTIME-REPRODUCED without an experiment.

## 4. Finding record format

Every finding must contain:

| Field | Required content |
|---|---|
| ID | Stable ID in the module namespace |
| Title | One concrete behavior/risk |
| Severity | Critical / High / Medium / Low / Informational; justify impact |
| Confidence | High / Medium / Low; separate from severity |
| Evidence | Source path + line range + commit/blob SHA |
| Trigger / preconditions | What must happen for the issue to manifest |
| Impact | User, data, security, reliability or performance consequence |
| Verification | Static / test-inventoried / test-executed / runtime-reproduced |
| Related paths | Callers, consumers, tests, protocol or lifecycle edges |
| State | Open / needs-evidence / accepted-risk / fixed-pending-verification / closed / rejected |
| Closure evidence | Required when closed or rejected |

A proposed fix is not closure evidence. A changed source SHA alone is not proof that a finding is fixed.

## 5. Cross-module tracing

For each important contract or lifecycle, trace both directions where applicable:

- definition → producer → transport/adapter → consumer → state mutation/render/persistence;
- startup → readiness/handshake → steady state → reconnect/recovery → teardown/disposal;
- validation → authorization → side effect → acknowledgement/error;
- asset manifest → build/index → load/decode → message → UI consumer;
- tests → actual code path and limitations.

Record edge evidence (path, lines, SHA) in the owning module record or dependency map. Do not assume a TypeScript interface validates untrusted runtime data.

## 6. Tests and runtime

- Reading a test is not running it.
- A passing unit test proves only the asserted path under that test's fixtures.
- Build/typecheck/lint/protocol generation are separate operations; record each separately.
- Runtime verification must name the host (VS Code extension, standalone browser/server, mobile webview, etc.), environment and reproduction steps.
- Do not run tests, install dependencies, build, modify source, or commit unless the user authorizes that execution scope.

## 7. Change control and audit integrity

- Audit-only documentation changes are allowed when requested; application source remains untouched.
- Generated files must be treated according to their generator/source-of-truth contract.
- Never silently rewrite historical findings. Append a correction or superseding evidence entry.
- Keep branch baselines explicit. Do not merge divergent branches as a shortcut to recover audit notes.
- End each session by updating the evidence log and the next-action pointer in the relevant module record.

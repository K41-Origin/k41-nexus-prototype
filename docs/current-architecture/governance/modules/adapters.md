# Adapters Module Audit Record

**Status:** STATIC AUDIT COMPLETE for the documented baseline; cross-module and dynamic verification remain OPEN.  
**Audit scope:** `adapters/vscode/` — 8/8 tracked files fetched and statically reviewed.  
**Historical source baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`.  
**Detailed owner:** [Adapters DEEP-AUDIT](../../adapters/DEEP-AUDIT.md).  
**File identity/coverage owner:** [Adapters FILE-REFERENCE](../../adapters/FILE-REFERENCE.md).

## Coverage already completed

- 8/8 adapter source files fetched and statically reviewed; large files were retrieved in line-range chunks.
- Relevant tests inspected: `server/__tests__/migrateVsCodeState.test.ts` and `server/__tests__/fileStateAdapter.test.ts`.
- Build/package entry and cross-module runtime seams inspected.
- Static findings A-01 through A-10 are recorded in the deep-audit document. Preserve their IDs and current states; do not create duplicates for the same behaviors.
- No tests, typecheck, lint, build, VS Code host, activation/deactivation, or live provider session was executed.

## Existing findings — ownership

| Finding | Existing technical focus | Current verification boundary |
|---|---|---|
| A-01 | Module-global host registrations are not reset on provider disposal | Static; dynamic lifecycle unverified |
| A-02 | `PixelAgentsViewProvider` is a high-coupling composition root | Architectural observation |
| A-03 | Agent lifecycle/removal ownership is split between adapter and server | Static; cross-layer invariant open |
| A-04 | Claude-specific launch path can leave partial side effects | Static; fault-injection/runtime unverified |
| A-05 | Webview/terminal listener ownership and disposal are incomplete/unclear | Static; VS Code lifecycle semantics unverified |
| A-06 | Restore grace-period timer is not tracked by runtime disposal | Static; shutdown timing unverified |
| A-07 | Migration clears Memento keys without awaiting updates | Static; async failure behavior unverified |
| A-08 | Migration equality relies on JSON serialization without schema validation | Static; malformed/reordered values not runtime-tested |
| A-09 | Workspace/project identity has multi-root/platform assumptions | Static; platform matrix unverified |
| A-10 | Uninstall cleanup is best-effort asynchronous work | Static; process completion semantics unverified |

## Cross-module links to preserve

- Server: F-01, F-02, F-03, F-04, F-05, F-08. See the relationship table in the adapter deep audit; do not reopen server findings without a new evidence question.
- Webview: message transport, readiness buffering, reconnect and UI snapshot handling.
- Core: host/terminal/provider contracts; adapter remains a VS Code host integration, not a generic provider-adapter registry.

## Mandatory continuation gate

Before any adapter source is revisited:

1. Retrieve the active source branch/commit and a complete recursive tree (`truncated: false`).
2. Compare all 8 current adapter path/blob-SHA pairs against [FILE-REFERENCE](../../adapters/FILE-REFERENCE.md).
3. If all 8 SHAs are unchanged, do not repeat their static review. Continue only a named cross-module question or a dynamic verification task explicitly authorized by the user.
4. If a SHA changed, inspect that file's diff and only the dependent paths required by the changed contract/lifecycle.
5. Preserve A-01–A-10; update an existing finding rather than duplicating it when the behavior matches.

## Next work

- Reconcile the adapter file SHAs at the active source commit before further investigation; the baseline above is historical provenance, not proof of current identity.
- Continue cross-layer lifecycle ownership and teardown tracing with server F-01/F-03/F-05 and webview reconnect/transport records.
- Activation/deactivation, repeated view resolution, terminal launch compensation, restore timer cancellation, multi-root behavior and uninstall completion remain unverified.
- Do not execute tests/build/lint/typecheck or VS Code runtime experiments unless explicitly authorized.

**Evidence classification:** coverage/findings above are DOC-RECORDED from the adapter audit documents. Current-source SHA reconciliation is OPEN until the 8-file ledger is compared with the active source tree.

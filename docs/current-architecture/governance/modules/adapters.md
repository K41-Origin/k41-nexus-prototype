# Adapters Module Audit Record

**Status:** STATIC AUDIT COMPLETE — 8/8 source files SHA-reconciled against the active source branch; cross-module and dynamic verification remain OPEN.  
**Audit scope:** `adapters/vscode/` — 8/8 tracked files fetched and statically reviewed.  
**Historical source baseline:** `main` — `4dd14d612fd37d89ffbbc977959f572395c4eec8`.  
**Latest SHA verification:** `main` — `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` (8/8 paths and blob SHAs unchanged).  
**Detailed owner:** [Adapters DEEP-AUDIT](../../adapters/DEEP-AUDIT.md).  
**File identity/coverage owner:** [Adapters FILE-REFERENCE](../../adapters/FILE-REFERENCE.md), including the 2026-11-25 SHA manifest.

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

## SHA reconciliation result — 2026-11-25

- Source branch: `main` at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`.
- Recursive tree: 856 entries / 739 blobs; `truncated: false`.
- Historical adapter baseline: `4dd14d612fd37d89ffbbc977959f572395c4eec8`.
- Comparison: 8/8 adapter paths unchanged; 0 added, 0 removed, 0 blob-SHA mismatches.
- Exact current blob SHAs and byte sizes are recorded in [FILE-REFERENCE](../../adapters/FILE-REFERENCE.md).
- **Conclusion:** the previous SHA-reconciliation gate is closed. No adapter source needs re-review because of source changes.

## Mandatory continuation gate

Before any adapter source is revisited:

1. Retrieve the active source branch/commit and a complete recursive tree (`truncated: false`).
2. Compare all 8 current adapter path/blob-SHA pairs against [FILE-REFERENCE](../../adapters/FILE-REFERENCE.md).
3. If all 8 SHAs are unchanged, do not repeat their static review. Continue only a named cross-module question or a dynamic verification task explicitly authorized by the user.
4. If a SHA changed, inspect that file's diff and only the dependent paths required by the changed contract/lifecycle.
5. Preserve A-01–A-10; update an existing finding rather than duplicating it when the behavior matches.

## Next work

- SHA reconciliation against active `main` is complete at `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`; do not repeat the 8-file static review while identities remain unchanged.
- Continue cross-layer lifecycle ownership and teardown tracing with server F-01/F-03/F-05 and webview reconnect/transport records.
- Activation/deactivation, repeated view resolution, terminal launch compensation, restore timer cancellation, multi-root behavior and uninstall completion remain unverified.
- Do not execute tests/build/lint/typecheck or VS Code runtime experiments unless explicitly authorized.

**Evidence classification:** prior coverage/findings are DOC-RECORDED; latest 8/8 path/blob identities are TREE-VERIFIED against active `main`. Dynamic behavior remains OPEN.

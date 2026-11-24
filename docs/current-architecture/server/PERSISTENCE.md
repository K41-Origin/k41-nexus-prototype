# Server Persistence

**Status:** STATIC SOURCE AUDIT DOCUMENTED — concurrency/runtime behavior pending verification

## Storage inventory

| Data | Location | Owner / implementation | Notes |
|---|---|---|---|
| Server discovery | `~/.pixel-agents/server.json` | `server.ts`, `serverConfig.ts` | Legacy single target; dual-written for old hook scripts |
| Multi-server registry | `~/.pixel-agents/servers/<pid>-<port>.json` | `server.ts` | One record per owned server; PID liveness and protocol validation |
| Standalone token | `~/.pixel-agents/standalone-token` | `serverToken.ts` | Persistent token, restrictive file mode; rotation option |
| Shared config | `~/.pixel-agents/config.json` | `configPersistence.ts` | Adapter settings namespaces plus machine-wide hooks, directories, external assets, recent characters |
| Agent state | `~/.pixel-agents/{vscode,standalone}-state.json` | `FileStateAdapter` | Persisted agents, seats and card order scoped by host namespace |
| Layout | `~/.pixel-agents/layout.json` | `layoutPersistence.ts` | Shared; revision-aware bundled default |
| Character profiles | `~/.pixel-agents/characters.json` | `characterProfilesStore.ts`, `characterProfilesPersistence.ts` | Shared validated cache and external-change watcher |
| Wardrobe items | `~/.pixel-agents/wardrobe-items.json` | Same | Shared validated cache and watcher |
| Conversation looks | `~/.pixel-agents/conversations.json` | `conversations.ts` | Session look/seat, capped history |
| Usage limits | `~/.pixel-agents/usage-limits.json` | `usageLimits.ts` | Last provider usage windows and update time |
| Claude settings | `~/.claude/settings.json` | Claude hook/status-line installers | User-owned external config; guarded mutation and backup |
| Claude hook script | `~/.pixel-agents/hooks/claude-hook.js` | `bundledScripts.ts` | Copied executable hook producer |
| Claude status-line script | `~/.pixel-agents/hooks/claude-statusline.js` | Same | Copied wrapper/reporting bridge |
| Claude transcripts/team files | `~/.claude/projects/**`, team config/sidecars | Provider + scanners | External source of truth; server reads and tails it |

## State ownership

- `AgentStateStore` owns the in-memory `Map<number, AgentState>`, emits lifecycle/broadcast events and serializes agents through `toPersistedAgent()`.
- Core `StateAdapter` is the persistence seam. `FileStateAdapter` implements it for standalone and VS Code namespaces.
- Shared machine-wide config is separate from adapter state. Adapter-specific preferences live under `vscode` or `standalone`; hook consent/preference, directories, external asset paths and recent character order are shared.
- Background-spawn child agents are intentionally not persisted as independent records; they are reconstructed from lead state and provider sidecars/notices.

## Atomicity, validation and corruption handling

- Several JSON writers use tmp + rename: config, adapter state, layout, profiles/wardrobe, usage, server discovery records and Claude settings.
- `jsonFileWatcher.ts` marks same-process writes to suppress self-notifications unless a caller explicitly requests watcher notification.
- Config/profile/card-order/registry/usage payloads have varying levels of runtime validation. `FileStateAdapter.readState()` validates top-level collection shapes but casts persisted agent/seat records rather than deeply validating every field.
- Missing/unreadable config falls back to defaults; malformed profile files fall back to default profiles; malformed external profile changes are ignored.
- Claude settings parsing failure is treated differently: mutation refuses to proceed rather than rewriting the user's settings from an empty object.
- Claude settings installer creates a one-time backup before modifying user-authored settings; preserves file mode; verifies the raw file did not change before rename and retries recognized concurrent changes. The final read/rename gap remains a race.

## Cross-process concurrency

Shared files can be read/written by embedded and standalone processes. The source uses atomic replacement and file watchers, but general `config.json` and adapter state writes are not protected by a cross-process lock or compare-and-swap. Atomic rename prevents partial-file reads; it does not prevent lost updates between independent read-modify-write cycles. This is a concrete area for runtime stress testing and future design review.

## Migration

- VS Code workspaceState migration is adapter-owned and runs before layout loading (see `adapters/vscode/migrateVsCodeState.ts`).
- Persisted agent fields include compatibility fallback for older directory names, repeat/hue values and background tool IDs.
- Server registry supports legacy `server.json` fallback during mixed-version compatibility.

## Limits

The audit did not inject crashes during writes, race two processes, validate permissions on each OS, or test backup restore. Treat atomicity and migration descriptions as source-level behavior, not proven durability guarantees.

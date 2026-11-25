# Server Providers

**Status:** STATIC SOURCE AUDIT DOCUMENTED

## Provider architecture

`server/src/providers/index.ts` exports the bundled hook providers and registers `hookProviders`. At the audited baseline the registry contains exactly one provider: `claudeProvider` (`id: claude`, kind `hook`). `hookProviderById()` resolves wire-supplied IDs and fails closed for unknown values.

The provider implements core `HookProvider` and supplies:
- Raw hook payload normalization to core `AgentEvent`.
- Hook install/uninstall/status and consent disclosure.
- Tool status formatting, permission-exempt tools and subagent tool names.
- Session roots, session filename pattern, launch command and inherited environment filtering.
- Conversation title extraction and model context-window mapping.
- Optional `TeamProvider` and `UsageBridge`.

## Claude hook event pipeline

Bundled `hooks/claude-hook.ts` reads JSON from stdin, calls `liveServers()`, and POSTs the original payload to each server's `/api/hooks/claude` endpoint. `serverRegistry.ts` reads the per-process registry, validates records and checks PIDs; legacy `server.json` is fallback only when registry has no live entries. Each POST is best-effort and bounded by timeout.

Claude-specific raw event fields are normalized only in `claude.ts`. Supported normalized cases include SessionStart/SessionEnd, PreToolUse, PostToolUse/PostToolUseFailure, Stop, SubagentStart/SubagentStop, PermissionRequest, Notification(permission_prompt/idle_prompt), TeammateIdle and TaskCompleted. UserPromptSubmit and TaskCreated normalize to null and are deliberately not installed in current hook event list.

`HookEventHandler` consumes normalized events. Transcript parsing remains a parallel data path and reads Claude JSONL records for tool inputs/results, usage, team tags and background task state.

## Consent and settings ownership

- `consentGate.ts` is provider-agnostic policy: whether to ask and how literal choices map to actions.
- `consentExecutor.ts` serializes answers within a process and executes the policy through host-provided effects.
- `configPersistence.ts` stores per-provider consent and hooks preference in machine-wide `config.json`.
- `claudeHookInstaller.ts` installs/removes only commands it recognizes as Pixel Agents-owned, preserving third-party entries and refusing malformed/unparseable settings.
- `claudeSettingsFile.ts` owns guarded read-modify-write, backup, atomic temp rename and concurrent-change retry.
- `claudeHookEntries.ts` and `commandIdentity.ts` define script command identity and exact-output predicates.

## Usage bridge

The optional status-line bridge wraps the user's existing Claude status-line command, passes its output through, and reports only normalized rate-limit windows and session context share to the server. `claudeUsageBridge.ts` validates those fields; `usageLimits.ts` persists last known windows and restricts usage broadcasts to privileged clients.

## Team integration

`claudeTeamProvider.ts` owns Claude-specific sidecar, team metadata, task notification, spawn result, teammate discovery and team config parsing. Generic runtime logic consumes core `TeamProvider` methods. Named background spawns can become teammate agents; unnamed spawns are watched through a shadow `AgentStateStore` and translated into existing subagent messages.

## Explicit boundary

This is an **event/session integration** for an external coding CLI. There is no outbound model API client, inference execution, model selection gateway, prompt orchestration or general-purpose LLM worker runtime in `server/` at this baseline. Do not describe the existing Claude provider as a model provider in that sense.

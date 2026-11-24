# UI State Management

**Status:** STATIC OWNERSHIP MAP — exact lifecycle closure remains in progress.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. State ownership layers

| Layer | Owner | State / responsibility | Durability |
|---|---|---|---|
| Office domain | OfficeState instance | Agents, seats, tile/furniture placement, scene entities and layout model | In-memory; layout persisted by host/server |
| Layout editor | EditorState and editor modules | Selected tool, draft changes, dirty state, undo/redo and editing interaction | In-memory draft; saved through host/server |
| React application | App/components/hooks | Modal visibility, panels, menus, drag state, selected views and transient interaction | In-memory component state |
| Character cast | castStore | Character profiles, custom wardrobe items, recency, local draft/save acknowledgement | Host/server persistence; client draft state |
| Usage | usageStore module singleton | Provider-keyed usage bridge status, usage windows and arrival sequence | In-memory snapshot; source data supplied by host/server |
| Conversation UI | useConversations/ConversationSearch | Recent list, query, page limit and current response | In-memory; transcript source remains server/filesystem |
| Transport | transport singleton | Connection state, subscriptions, pending outbound messages and reconnect timer | Runtime-only |

## 2. Main state ingress

useExtensionMessages subscribes to the MessageTransport and updates React state and OfficeState. It also coordinates initial handshake, existing-agent reconciliation, reconnect snapshots, layout loading, asset loading, directories, settings, hooks/consent, terminal availability and agent activity.

Delegated message ownership:
- characters/castMessages.ts handles characterProfilesLoaded, recentCharactersLoaded and characterSpritesLoaded.
- usage/usageStore.ts handles usageBridgeStatus and usageLimits.
- conversations/useConversations.ts handles conversationsLoaded; ConversationSearch.tsx handles conversationSearchResults.
- DebugView.tsx subscribes for agentDiagnostics.

## 3. State egress and persistence

UI actions send ClientMessage commands through transport. The host/server is authoritative for persisted settings, layout, seats/card order, directories, character profiles and session operations. The UI may update optimistically for interaction, but acknowledgements/snapshots reconcile shared state.

VS Code postMessage has no transport reconnect lifecycle. The adapter's webviewReady handshake sends the initial snapshot. Standalone WebSocket reconnect causes the UI to request/reconcile state through the application message hook. Conversation UI requests lists/search from the standalone server; terminal controls are standalone-only in the current host split.

## 4. Consistency and lifecycle observations

- CastStore preserves a dirty local draft when external profiles arrive; a later save can overwrite that external update (WV-C09, last-writer-wins semantics).
- App's isEditDirty callback is passed into a long-lived useExtensionMessages effect whose dependency list does not refresh the callback (WV-APP-01 candidate). Reconnect layout handling may therefore consult stale dirty state; runtime reproduction remains pending.
- Module-level Office catalog/sprite caches and usageStore have process/module lifetime rather than component lifetime. Their reset/bounds semantics differ.
- useExtensionMessages is a broad orchestration hook; extraction boundaries should follow message/state ownership rather than splitting by arbitrary file size.
- Transport subscriptions generally return unsubscribe functions. Global mouse listeners, audio context/worker resources and some caches need additional lifecycle verification.

## 5. Persistence boundary

The UI does not own the authoritative filesystem/database. The host/server owns durable config, layout, character profiles, agent state and transcript-derived conversations. Browser localStorage is used for selected client preferences such as radio settings, not as a replacement for host persistence.

## 6. Audit boundary

This is a static ownership map. No React runtime, reconnect fault injection, persistence race test or test suite was executed. See DEEP-AUDIT.md and PROTOCOL-MATRIX.md for unresolved lifecycle and message validation work.

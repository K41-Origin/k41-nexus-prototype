# UI State Management

**Status:** STATIC OWNERSHIP MAP — exact lifecycle closure remains in progress.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. State ownership layers

| Layer | Owner | State / responsibility | Durability |
|---|---|---|---|
| Office domain | OfficeState instance | Agents, seats, tile/furniture placement, scene entities and layout | In-memory; layout persisted by host/server |
| Layout editor | EditorState and editor modules | Tool, draft, dirty state, undo/redo and editing interaction | In-memory draft; saved through host/server |
| React application | App/components/hooks | Modal/panel/menu visibility, drag state and transient interaction | In-memory |
| Character cast | castStore | Profiles, custom wardrobe items, recency, draft/save acknowledgement | Host/server persistence plus client draft |
| Usage | usageStore singleton | Provider-keyed bridge status, usage windows and arrival sequence | In-memory snapshot |
| Conversation UI | useConversations/ConversationSearch | Recent list, query, page limit and response state | In-memory; transcripts remain server/filesystem-owned |
| Transport | transport singleton | Connection state, subscriptions, pending outbound messages and reconnect timer | Runtime-only |

## 2. Main state ingress

useExtensionMessages subscribes to MessageTransport and updates React state and OfficeState. It coordinates handshake, existing-agent reconciliation, reconnect snapshots, layout/assets, directories, settings, hooks/consent, terminal availability and agent activity.

Delegated ownership:
- characters/castMessages.ts: characterProfilesLoaded, recentCharactersLoaded, characterSpritesLoaded.
- usage/usageStore.ts: usageBridgeStatus, usageLimits.
- conversations/useConversations.ts: conversationsLoaded.
- conversations/ConversationSearch.tsx: conversationSearchResults.
- components/DebugView.tsx: agentDiagnostics.

## 3. State egress and persistence

UI actions send ClientMessage commands through transport. Host/server is authoritative for persisted settings, layout, seats/card order, directories, character profiles and session operations. UI may update optimistically, then reconcile with acknowledgements/snapshots.

VS Code postMessage has no transport reconnect lifecycle; the adapter's webviewReady handshake sends the initial snapshot. Standalone WebSocket reconnect triggers application resynchronization. Conversation list/search requests are server-backed; terminal drawer/session controls are standalone-specific.

## 4. Consistency and lifecycle observations

- WV-C09: castStore preserves a dirty local draft when external profiles arrive; a later save can overwrite the external update (last-writer-wins).
- WV-APP-01 (Medium–High, static finding): App creates isEditDirty from editor.isEditMode/isDirty and passes it to useExtensionMessages. The subscription effect reads the callback but depends only on getOfficeState, with exhaustive-deps suppressed. It retains the first callback closure (normally false/false), so subsequent dirty editor state may not block external/reconnect layoutLoaded snapshots. Runtime reproduction remains pending.
- WV-RESYNC-01 (Medium, static finding): snapshot seat metadata can be lost when the current layout does not contain the requested seat before the next layout snapshot. GOV-WEBVIEW-012 extends the same canonical finding to VS Code first hydration: early agentCreated can create the character before existingAgents is buffered; the later pending restore is blocked by OfficeState.addAgent's existing-ID guard, so separate persisted seat metadata may not be applied.
- WV-RESYNC-02 (Medium, static finding): all ClientMessages sent while the standalone control socket is not OPEN are queued and replayed after reconnect without expiry or idempotency classification. A delayed launch/resume/close or stale settings write is possible; runtime reproduction remains open.
- Module-level Office catalogs/sprite caches and usageStore have module lifetime rather than component lifetime; bounds/reset semantics differ.
- useExtensionMessages is a broad orchestration hook; extraction boundaries should follow message/state ownership.
- Global mouse listeners, audio context/worker resources and some caches need further lifecycle verification.

## 5. Persistence boundary

The UI does not own the authoritative filesystem/database. Host/server owns durable config, layout, character profiles, agent state and transcript-derived conversations. Browser localStorage is used for selected client preferences such as radio settings, not as a replacement for host persistence.

## 6. Audit boundary

Static ownership map plus GOV-WEBVIEW-012 cross-layer lifecycle trace. No React runtime, reconnect fault injection, persistence race test or test suite was executed. See DEEP-AUDIT.md, TRANSPORT.md and PROTOCOL-MATRIX.md.

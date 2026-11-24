# Webview UI — Control-Plane Protocol Matrix

**Status:** PARTIAL — top-level unions and major ingress/egress paths verified; per-variant producer and persistence trace remains in progress.  
**Protocol source:** core/asyncapi.yaml (source of truth)  
**Generated TypeScript:** core/src/messages.ts (generated; do not edit)  
**Audit baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Protocol boundaries

The AsyncAPI document defines one bidirectional WebSocket control-plane channel at /ws. The same message unions are used by the VS Code webview bridge and standalone browser transport.

Do not conflate this control plane with raw terminal frames: terminal byte/frame streaming uses a separate WebSocket path and lifecycle.

The generated unions contain:
- 46 ServerMessage variants.
- 34 ClientMessage variants.
- 80 named interfaces corresponding to the union members; no missing interface was found when comparing union member names with generated interface declarations.

## 2. Server-to-client messages

| Variant | UI consumer / route | Notes |
|---|---|---|
| providerCapabilities | useExtensionMessages -> setProviderCapabilities | Tool taxonomy used by animation/subagent rendering |
| agentCreated | useExtensionMessages -> OfficeState/agent restore | Lifecycle; agent card/character creation |
| agentClosed | useExtensionMessages -> OfficeState and agent maps | Lifecycle cleanup |
| agentSelected | useExtensionMessages | Active agent selection |
| existingAgents | useExtensionMessages -> reconcileExistingAgents/restoreAgent | Initial/reconnect reconciliation |
| agentStatus | useExtensionMessages | Agent activity/waiting state |
| agentToolStart | useExtensionMessages | Tool activity and background spawn tracking |
| agentToolDone | useExtensionMessages | Tool completion |
| agentToolsClear | useExtensionMessages | Clears turn tools; background subagent lifecycle is distinct |
| agentToolPermission | useExtensionMessages | Permission-wait state |
| agentToolPermissionClear | useExtensionMessages | Permission-wait clear |
| subagentToolStart | useExtensionMessages | Child tool activity |
| subagentToolDone | useExtensionMessages | Child tool completion |
| subagentClear | useExtensionMessages | Child lifecycle cleanup |
| subagentToolPermission | useExtensionMessages | Child permission-wait state |
| agentTeamInfo | useExtensionMessages | Team/teammate identity |
| agentContextUsage | useExtensionMessages -> contextGauge | Context usage display |
| layoutLoaded | useExtensionMessages -> layout/editor state | Snapshot and import/external layout updates |
| furnitureAssetsLoaded | useExtensionMessages -> buildDynamicCatalog | Furniture metadata and sprite catalog |
| characterProfilesLoaded | handleCastMessage -> castStore | parseCharacterProfiles and optional parseWardrobeItems |
| recentCharactersLoaded | handleCastMessage -> castStore | Recent roster state |
| characterSpritesLoaded | handleCastMessage -> setExternalCharacterSprites | External character sprites |
| petSpritesLoaded | useExtensionMessages -> setPetTemplates | Pet sprite templates |
| floorTilesLoaded | useExtensionMessages -> setFloorSprites | Floor sprite data |
| wallTilesLoaded | useExtensionMessages -> setWallSprites | Wall sprite data |
| carpetTilesLoaded | useExtensionMessages -> setCarpetSprites | Carpet sprite data |
| officeAccess | useExtensionMessages | Read-only mode gate in UI |
| settingsLoaded | useExtensionMessages | Initial settings and preferences |
| bypassPermissionsUpdated | useExtensionMessages | Permission posture update |
| hooksStatus | useExtensionMessages | Provider-keyed install state and arrival sequence |
| hooksConsentRequest | useExtensionMessages | Provider-keyed consent queue |
| usageBridgeStatus | handleUsageMessage -> usageStore | Shallow checks: providerId string, installed boolean |
| usageLimits | handleUsageMessage -> usageStore | Shallow check: providerId string; nested windows not fully validated here |
| externalAssetDirectoriesUpdated | useExtensionMessages | External asset directory list |
| areaMappingsLoaded | useExtensionMessages | Directory-to-Area mappings |
| cardOrderLoaded | useExtensionMessages | Agent card ordering |
| directoriesLoaded | useExtensionMessages and useConversations refresh trigger | Directory state and conversation list invalidation |
| directorySaved | useExtensionMessages | Point-to-point accepted save acknowledgement |
| directoryRejected | useExtensionMessages | Point-to-point refused save response |
| directorySuggestions | useExtensionMessages | Directory modal suggestions |
| conversationsLoaded | useConversations | Conversation sidebar list |
| conversationSearchResults | ConversationSearch / conversation search state | Search response; exact producer path still to trace |
| terminalAvailability | useExtensionMessages | Standalone PTY availability; VS Code host owns terminals separately |
| terminalSessionOpened | useExtensionMessages | Standalone terminal session lifecycle |
| terminalSessionClosed | useExtensionMessages | Standalone terminal session lifecycle |
| agentDiagnostics | DebugView and useExtensionMessages transport subscription | Point-to-point diagnostic response |

The UI consumption is distributed rather than centralized exclusively in useExtensionMessages:
- Character cast messages are delegated to characters/castMessages.ts.
- Usage messages are delegated to usage/usageStore.ts.
- Conversation messages are handled by conversations hooks/components.
- Diagnostics are consumed by DebugView.
- The remaining principal office/agent/asset/settings messages are handled in useExtensionMessages.ts.

## 3. Client-to-server messages

| Variant | Main ingress | Scope / behavior |
|---|---|---|
| webviewReady | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Handshake; flush buffered broadcasts and send initial snapshot |
| launchAgent | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Launch host-specific terminal/session |
| focusAgent | VS Code PixelAgentsViewProvider | VS Code terminal focus; intentionally absent from standalone server handler |
| closeAgent | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Close agent/session |
| saveAgentSeats | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist palette/repeat/seat assignment |
| saveLayout | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist layout; VS Code watcher and standalone broadcast semantics differ |
| saveCharacterProfiles | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Shared profile validation/persistence path |
| setSoundEnabled | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist preference |
| setLastSeenVersion | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist preference |
| setAlwaysShowLabels | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist preference |
| setGhostHeadlessAgents | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist preference |
| setHooksEnabled | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Provider-keyed hook preference |
| hooksConsentResponse | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Provider-keyed consent answer |
| setUsageBridgeEnabled | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Provider-keyed usage bridge preference |
| setHooksInfoShown | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist first-run information state |
| setWatchAllSessions | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist watcher preference |
| exportLayout | VS Code PixelAgentsViewProvider | Host-native save dialog; not handled by standalone clientMessageHandler |
| importLayout | VS Code PixelAgentsViewProvider | Host-native open dialog; not handled by standalone clientMessageHandler |
| exportFile | VS Code PixelAgentsViewProvider | Host-native save dialog for generated file; not handled by standalone clientMessageHandler |
| openSessionsFolder | VS Code PixelAgentsViewProvider | Opens host project/session folder; not handled by standalone clientMessageHandler |
| addExternalAssetDirectory | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Host-specific picker/path handling |
| removeExternalAssetDirectory | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Remove configured asset directory |
| saveAreaMappings | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist Directory-to-Area mappings |
| saveCardOrder | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist shared card order |
| setShowAreas | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist rendering preference |
| setBypassPermissions | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist host permission posture |
| setShowHostDirectories | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Persist host-directory visibility |
| saveDirectory | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Validate and persist user directory |
| removeDirectory | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Remove user directory |
| requestDirectorySuggestions | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Point-to-point suggestions response |
| requestConversations | standalone clientMessageHandler | Conversation sidebar request; VS Code path is handled by shared server/host message handling where wired |
| searchConversations | standalone clientMessageHandler | Conversation search request |
| resumeConversation | standalone clientMessageHandler | Relaunch past session |
| requestDiagnostics | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Point-to-point diagnostics response |

The standalone server handler has 29 explicit switch cases. The five ClientMessage variants absent from that switch are focusAgent, exportLayout, importLayout, exportFile and openSessionsFolder; these are host-specific VS Code actions and are handled in PixelAgentsViewProvider. This is a transport/host boundary, not automatically a protocol defect.

## 4. Transport ingress and runtime validation

### Standalone WebSocket

- webSocketTransport.ts parses JSON then casts it to ServerMessage.
- There is no comprehensive runtime schema validation at this transport boundary.
- One subscriber throwing inside the shared onmessage try block aborts iteration; the exception is swallowed by the catch intended for malformed JSON (WV-TR-01).
- send() queues ClientMessage values while the socket is not OPEN and flushes them on open.
- dispose() clears reconnect timer, closes socket, drops handlers and pending messages.

### VS Code postMessage

- postMessageTransport.ts sends through acquireVsCodeApi().postMessage.
- Incoming window message data is cast to ServerMessage without runtime schema validation.
- The transport reports connected for its lifetime; ready is already resolved and onStateChange is a no-op.
- The VS Code adapter's onDidReceiveMessage receives an untrusted runtime object and dispatches by message.type; TypeScript interfaces do not validate runtime payloads.

### Validation evidence

Validation is inconsistent across boundaries:
- Character profiles use parseCharacterProfiles; custom wardrobe items use parseWardrobeItems.
- Usage store checks providerId and installed for usageBridgeStatus; usageLimits only checks providerId before casting nested data.
- Some server client-message cases validate values/ranges before persistence or side effects.
- saveLayout accepts a truthy layout then casts it; saveAreaMappings checks only that mappings is a non-null object before casting to Record<string,string[]>.
- saveAgentSeats has targeted numeric/range validation, but nested shape validation is not uniformly schema-driven.
- Directory handlers validate path existence/type and return directoryRejected on refusal.
- The top-level read-only gate is fail-closed, but does not substitute for per-message schemas.

This correlates with server finding F-08 and webview finding WV-C08. The exact validation matrix per field and all side effects remains open.

## 5. Ordering and handshake invariants

VS Code handshake sends provider capabilities, settings and area mappings before restoring agents; area mappings must arrive before agent creation so preferred Area seat placement can be applied. Card order is sent before restored agents. Character profiles/recent characters are sent before dependent assets/layout. Furniture assets are loaded before layout is sent.

The adapter buffers broadcasts until webviewReady; the buffer is capped at 1,000 and drops the oldest entry on overflow. On readiness, buffered messages flush before initial snapshot messages. These orderings are behaviorally significant and must be preserved in future architecture work.

## 6. Open protocol audit tasks

1. Trace the producer of every ServerMessage variant to the exact server/adapter function and determine broadcast vs point-to-point recipient.
2. Trace every ClientMessage producer from UI component/hook to transport and host ingress.
3. Record field-level validation, authorization/read-only behavior, persistence and side effects per ClientMessage.
4. Verify the VS Code and standalone handshake order independently.
5. Confirm whether conversation search response is consumed and how stale requests are correlated.
6. Add explicit runtime validation strategy only after the audit; no implementation changes are authorized in this phase.

## 7. Exclusions

No source code was changed. No tests, build, lint, typecheck or live WebSocket/postMessage runtime experiments were executed. This matrix is a static routing map, not proof that every message path has been exercised.

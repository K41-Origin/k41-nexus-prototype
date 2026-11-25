# Webview UI — Control-Plane Protocol Matrix

**Status:** STATIC TRACE COMPLETE — all 34 ClientMessage and 46 ServerMessage variants mapped to UI producers/consumers, host ingress/producers and delivery scope; field-level validation and multi-client consistency remain OPEN.  
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
| conversationSearchResults | ConversationSearch.tsx | Exact response consumer verified; query, directoryPath and limit must match latest request |
| terminalAvailability | useExtensionMessages | Standalone PTY availability; VS Code host owns terminals separately |
| terminalSessionOpened | useExtensionMessages | Standalone terminal session lifecycle |
| terminalSessionClosed | useExtensionMessages | Standalone terminal session lifecycle |
| agentDiagnostics | DebugView and useExtensionMessages transport subscription | Point-to-point diagnostic response |

The UI consumption is distributed rather than centralized exclusively in useExtensionMessages:
- Character cast messages are delegated to characters/castMessages.ts.
- Usage messages are delegated to usage/usageStore.ts.
- Conversation messages are handled by conversations hooks/components.
- Diagnostics are consumed by DebugView.
- ConversationSearch.tsx independently subscribes to conversationSearchResults and rejects responses whose query, directoryPath or limit do not match the latest request.
- The remaining principal office/agent/asset/settings messages are handled in useExtensionMessages.ts.

## 3. Client-to-server messages

| Variant | Main ingress | Scope / behavior |
|---|---|---|
| webviewReady | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Handshake; flush buffered broadcasts and send initial snapshot |
| launchAgent | VS Code PixelAgentsViewProvider; standalone clientMessageHandler | Launch host-specific terminal/session |
| focusAgent | App.tsx (both runtimes) | VS Code PixelAgentsViewProvider focuses the host terminal; standalone server intentionally no-ops it while App resolves its own drawer/tab focus locally. |
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

The standalone server handler has 29 explicit switch cases. Five variants have no explicit standalone switch case: focusAgent, exportLayout, importLayout, exportFile and openSessionsFolder. Four are VS Code-only host actions with browser/local alternatives; focusAgent is emitted in both runtimes, but standalone intentionally no-ops the server side while App handles local drawer/tab focus. This is a transport/host boundary, not automatically a protocol defect.

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

## 5. Conversation request/response path — verified

- useConversations sends requestConversations while the sidebar is open; the server requires both ctx.ptyManager and a conversationProvider, then replies with conversationsLoaded to the requesting socket.
- ConversationSearch.tsx sends searchConversations with query, optional directoryPath and limit. The server normalizes the query with conversationQueryFrom(), searches the configured directories/live sessions and replies with conversationSearchResults to the requesting socket.
- ConversationSearch.tsx accepts a response only when query, directoryPath and limit still match the current request, preventing stale responses from replacing the active search.
- resumeConversation is accepted only when runtime, PTY manager and conversation provider are available; resumeRequest() validates/resolves the request and launchStandaloneAgent() launches the resumed session.
- PixelAgentsViewProvider does not implement these three conversation request types. The current UI gates the conversation sidebar/menu on terminalAvailable, which is a standalone PTY capability; this is an intentional host capability split in the inspected code, not a missing VS Code conversation handler.

These request/response messages are point-to-point, unlike shared agent/layout/settings broadcasts.

## 6. Ordering and handshake invariants

VS Code handshake sends provider capabilities, settings and area mappings before restoring agents; area mappings must arrive before agent creation so preferred Area seat placement can be applied. Card order is sent before restored agents. Character profiles/recent characters are sent before dependent assets/layout. Furniture assets are loaded before layout is sent.

The adapter buffers broadcasts until webviewReady; the buffer is capped at 1,000 and drops the oldest entry on overflow. On readiness, buffered messages flush before initial snapshot messages. These orderings are behaviorally significant and must be preserved in future architecture work.

## 7. Open protocol audit tasks

1. Static producer/consumer routing is now mapped for all 46 ServerMessage and 34 ClientMessage variants; continue only where field-level validation, side effects or cross-client behavior remain uncertain.
2. Reconcile shared-state fan-out gaps WV-PROTO-01 and WV-PROTO-02; determine the intended multi-client consistency contract.
3. Complete per-field validation/authorization matrix, reusing WV-C08 and server F-08 rather than duplicating them.
4. Dynamic protocol, reconnect and multi-client verification remains OPEN and requires explicit authorization; no implementation changes are authorized in this phase.

## 8. Exclusions

No source code was changed. No tests, build, lint, typecheck or live WebSocket/postMessage runtime experiments were executed. This matrix is a static routing map, not proof that every message path has been exercised.


## 9. Complete ClientMessage producer → transport → host ingress map

All 34 variants in core/asyncapi.yaml are accounted for below. The webview uses the same transport facade for VS Code postMessage and standalone WebSocket; host-specific branches are intentional unless noted.

| ClientMessage | UI producer and source evidence | VS Code ingress | Standalone ingress / behavior |
|---|---|---|---|
| webviewReady | useExtensionMessages.ts:911, 916 | PixelAgentsViewProvider.ts:675; flush buffer and send host snapshot | clientMessageHandler.ts:173; send standalone snapshot |
| launchAgent | useLaunchActions.ts:15–19 | PixelAgentsViewProvider.ts:478; launch VS Code terminal | clientMessageHandler.ts:177; requires runtime + PTY manager |
| focusAgent | App.tsx:263–265, 384–389, 413–416 | PixelAgentsViewProvider.ts:518; focus terminal/lead terminal | clientMessageHandler.ts:491–495 default no-op; App handles standalone drawer/tab focus locally |
| closeAgent | App.tsx:376–378; DebugView.tsx:122–125 | PixelAgentsViewProvider.ts:531 | clientMessageHandler.ts:254 |
| saveAgentSeats | useExtensionMessages.ts:180–182; invoked at :413 and :466 | PixelAgentsViewProvider.ts:543 | clientMessageHandler.ts:300 |
| saveLayout | useEditorActions.ts:143–149, 495–505; App.tsx:495–498 migration | PixelAgentsViewProvider.ts:566 | clientMessageHandler.ts:278; writes with reportToWatchers=true |
| saveCharacterProfiles | characters/castStore.ts:19–22 | PixelAgentsViewProvider.ts:550; shared persistence handler | clientMessageHandler.ts:286; same shared persistence handler |
| setSoundEnabled | components/SettingsModal.tsx:201–204 | PixelAgentsViewProvider.ts:568 | clientMessageHandler.ts:343 |
| setLastSeenVersion | App.tsx:223–230 | PixelAgentsViewProvider.ts:570 | clientMessageHandler.ts:347 |
| setAlwaysShowLabels | App.tsx:248–251 | PixelAgentsViewProvider.ts:572 | clientMessageHandler.ts:351 |
| setGhostHeadlessAgents | App.tsx:258–261 | PixelAgentsViewProvider.ts:574 | clientMessageHandler.ts:355 |
| setHooksEnabled | App.tsx:988–1002 | PixelAgentsViewProvider.ts:576 | clientMessageHandler.ts:366; provider ID checked, enabled value is cast |
| hooksConsentResponse | hooks/useIntroTour.ts:69–74 | PixelAgentsViewProvider.ts:594 | clientMessageHandler.ts:388; choice is narrowed by consentActionFor |
| setUsageBridgeEnabled | usage/useUsageBridge.ts:46–51 | PixelAgentsViewProvider.ts:582 | clientMessageHandler.ts:376; provider bridge + boolean checked |
| setHooksInfoShown | App.tsx:697–710 | PixelAgentsViewProvider.ts:599 | clientMessageHandler.ts:401 |
| setWatchAllSessions | App.tsx:982–986 | PixelAgentsViewProvider.ts:640 | clientMessageHandler.ts:359; persists and changes runtime watcher ref |
| exportLayout | components/SettingsModal.tsx:104–108; emitted only outside browser runtime | PixelAgentsViewProvider.ts:994; native save dialog | No standalone handler; browser uses exportLayoutToFile locally |
| importLayout | components/SettingsModal.tsx:118–121; emitted only outside browser runtime | PixelAgentsViewProvider.ts:1042; native open dialog + shallow layout gate | No standalone handler; browser reads and applies the file locally |
| exportFile | characters/ui/files.ts:14–32; emitted only outside browser runtime | PixelAgentsViewProvider.ts:564; host save dialog | No standalone handler; browser downloads the Blob locally |
| openSessionsFolder | components/SettingsModal.tsx:93–98 | PixelAgentsViewProvider.ts:989 | No standalone handler; host-only action |
| addExternalAssetDirectory | components/SettingsModal.tsx:157–174 | PixelAgentsViewProvider.ts:1008; host folder picker or supplied path | clientMessageHandler.ts:405; path is truthy-checked then persisted |
| removeExternalAssetDirectory | components/SettingsModal.tsx:187–190 | PixelAgentsViewProvider.ts:1029 | clientMessageHandler.ts:418 |
| saveAreaMappings | App.tsx:282–303; hooks/useDirectoryEditor.ts:64–78 | PixelAgentsViewProvider.ts:635 | clientMessageHandler.ts:436 |
| saveCardOrder | hooks/useExtensionMessages.ts:248–251 | PixelAgentsViewProvider.ts:547 | clientMessageHandler.ts:429 |
| setShowAreas | App.tsx:307–313 | PixelAgentsViewProvider.ts:601 | clientMessageHandler.ts:447 |
| setBypassPermissions | App.tsx:1007–1012 | PixelAgentsViewProvider.ts:624 | clientMessageHandler.ts:453; boolean checked and read-back broadcast |
| setShowHostDirectories | App.tsx:1013–1018 | PixelAgentsViewProvider.ts:619 | clientMessageHandler.ts:466; boolean checked and directories broadcast |
| saveDirectory | hooks/useDirectoryEditor.ts:138–145 | PixelAgentsViewProvider.ts:605–618 | clientMessageHandler.ts:475–489; shared validator/persistence |
| removeDirectory | hooks/useDirectoryEditor.ts:159–162 | PixelAgentsViewProvider.ts:605–618 | clientMessageHandler.ts:475–489; shared validator/persistence |
| requestDirectorySuggestions | hooks/useDirectoryEditor.ts:115–124 | PixelAgentsViewProvider.ts:605–618 | clientMessageHandler.ts:475–489; point-to-point reply |
| requestConversations | conversations/useConversations.ts:47–57 | Not implemented by PixelAgentsViewProvider; UI is gated by standalone terminal availability | clientMessageHandler.ts:203; requires PTY manager/provider |
| searchConversations | conversations/ConversationSearch.tsx:90–104 | Not implemented by PixelAgentsViewProvider; UI is gated by standalone terminal availability | clientMessageHandler.ts:217; validated query, point-to-point result |
| resumeConversation | conversations/useConversations.ts:79–83; hooks/useLaunchActions.ts:22–27 | Not implemented by PixelAgentsViewProvider; UI is gated by standalone terminal availability | clientMessageHandler.ts:234; resumeRequest validates/resolves before launch |
| requestDiagnostics | components/DebugView.tsx:75–81 | PixelAgentsViewProvider.ts:983 | clientMessageHandler.ts:273; point-to-point response |

**Ingress completeness:** 29 variants are handled by the standalone clientMessageHandler switch. Five have no explicit standalone case: focusAgent, exportLayout, importLayout, exportFile and openSessionsFolder. Four are VS Code-only host actions; focusAgent is still emitted in standalone but intentionally no-ops server-side while App handles local focus. No other ClientMessage variant was found without a producer and ingress/intent mapping in this static pass.

## 10. Complete ServerMessage producer and delivery map

All 46 variants in the AsyncAPI ServerMessage union are accounted for. “Broadcast” means AgentStateStore.broadcast is fanned to every connected standalone WebSocket by httpServer and to the VS Code webview through the adapter's store listener. Direct messages are scoped to the requesting socket/webview unless noted.

| ServerMessage variant(s) | Producer / source evidence | Delivery and lifecycle |
|---|---|---|
| providerCapabilities | clientMessageHandler.ts:586–591; PixelAgentsViewProvider.ts:685–692 | Point-to-point during webviewReady, before agent activity |
| agentCreated | agentMessages.ts:8–22; AgentStateStore agentAdded → httpServer.ts:264–265 / PixelAgentsViewProvider.ts:161–163 | Per connected socket/webview on store agentAdded |
| agentClosed | httpServer.ts:268–270; PixelAgentsViewProvider.ts:164–166 | Per connected socket/webview on store agentRemoved |
| agentSelected | PixelAgentsViewProvider.ts:1064–1071 | Direct to the VS Code webview when active terminal changes; no standalone producer |
| existingAgents | clientMessageHandler.ts:723–752; adapters/vscode/agentManager.ts:495–531 | Point-to-point handshake snapshot; sent before layoutLoaded and materialized by the UI after layout |
| agentStatus, agentToolStart, agentToolDone, agentToolsClear, agentToolPermission | hookEventHandler.ts; transcriptParser.ts; timerManager.ts | Store broadcast; current activity is separately replayed to a newly connected client |
| agentToolPermissionClear | fileWatcher.ts:230–241 | Store broadcast when transcript data clears a non-hook permission wait |
| subagentToolStart, subagentToolDone, subagentClear, subagentToolPermission | hookEventHandler.ts; transcriptParser.ts; subagentWatch.ts; fileWatcher.ts:905–906; timerManager.ts | Store broadcast; background subagent activity is replayed/reconstructed on connect |
| agentTeamInfo | transcriptParser.ts; hookEventHandler.ts; agentRuntime.ts; fileWatcher.ts:885–897; agentActivityResend.ts:19–31 | Store broadcast for changes; point-to-point replay on connect |
| agentContextUsage | contextUsage.ts:93–110; agentActivityResend.ts:84–91 | Store broadcast for updates; point-to-point replay on connect |
| layoutLoaded | clientMessageHandler.ts:754–757; adapters/vscode/agentManager.ts:544–553; PixelAgentsViewProvider.ts:1197–1202, 1042–1057 | Point-to-point handshake, layout watcher or host import; standalone save uses reportToWatchers so the VS Code watcher can receive another client's write |
| furnitureAssetsLoaded, characterProfilesLoaded, recentCharactersLoaded, characterSpritesLoaded, petSpritesLoaded, floorTilesLoaded, wallTilesLoaded, carpetTilesLoaded | clientMessageHandler.ts:612–640; characterProfilesPersistence.ts:70–82, 123–138; recentCharacters.ts:18–30; assetLoader.ts; cli.ts:382–415; PixelAgentsViewProvider.ts:891–979 | Handshake/reload messages go to the connecting/requesting client; profile and recent-character changes also use store broadcast where the shared persistence/launch path invokes it |
| officeAccess, settingsLoaded, areaMappingsLoaded, cardOrderLoaded, terminalAvailability | clientMessageHandler.ts:581–610, 649–653, 696–701, 744–757, 783–801; PixelAgentsViewProvider.ts:700–746, 784–791 | Point-to-point handshake snapshots; settingsLoaded and selected snapshots are also used to restore refused read-only UI changes |
| bypassPermissionsUpdated | clientMessageHandler.ts:453–463; PixelAgentsViewProvider.ts:624–633 | Store broadcast after read-back of persisted permission posture |
| hooksStatus, hooksConsentRequest | clientMessageHandler.ts:506–527, 577–688; providers/hook/consentGate.ts:35–44; PixelAgentsViewProvider.ts:753–779 | Handshake status/consent is direct; toggle/consent result status is currently sent to the initiating client |
| usageBridgeStatus, usageLimits | usageLimits.ts:123–150; clientMessageHandler.ts:691–694; httpServer.ts:201–204 | Handshake snapshot is privileged-only; usage report updates broadcast to privileged clients only |
| externalAssetDirectoriesUpdated | clientMessageHandler.ts:405–425, 844–850; cli.ts:382–415; PixelAgentsViewProvider.ts:1008–1041 | Direct to requesting webview/socket with reloaded sprites; not a store broadcast |
| directoriesLoaded | directories.ts:290–295; clientMessageHandler.ts:142–146, 466–472; PixelAgentsViewProvider.ts:158–159, 619–623 and sendDirectories | Store broadcast for standalone user-directory/show-host changes; direct/buffered to the single VS Code webview |
| directorySaved, directoryRejected, directorySuggestions | directories.ts:236–282 | Point-to-point reply to the requesting client; successful save/delete additionally broadcasts directoriesLoaded |
| conversationsLoaded, conversationSearchResults | clientMessageHandler.ts:203–231 | Point-to-point response to the requesting socket; standalone PTY/provider capability required |
| terminalSessionOpened, terminalSessionClosed | standaloneAgentLauncher.ts:261–275; clientMessageHandler.ts:764–773 | Store broadcast for live session lifecycle; opened is also replayed point-to-point on handshake |
| agentDiagnostics | clientMessageHandler.ts:273–275; agentDiagnostics.ts:32–55; PixelAgentsViewProvider.ts:983–988 | Point-to-point response to the requesting client |

**Consumer reconciliation:** the existing ServerMessage consumer table in section 2 accounts for all 46 variants. Character cast and usage messages are delegated to their stores; conversations and diagnostics own their own subscriptions; the remaining office state is handled by useExtensionMessages.ts.

## 11. Authorization, validation, persistence and fan-out summary

### Transport and authorization

- Standalone httpServer.ts:282–301 parses JSON and casts to Record<string, unknown>; there is no union/schema validator at the WebSocket ingress.
- clientMessageHandler.ts:164–170 is the server-side authorization gate: when privileged is not true, only webviewReady is allowed; all other ClientMessage variants are refused. officeAccess is a UI presentation signal, not the authorization boundary.
- httpServer.ts:272–275 filters PRIVILEGED_BROADCAST_TYPES for read-only sockets; usageBridgeStatus and usageLimits are withheld. The initial usage snapshot is also sent only when ctx.privileged is true.
- VS Code postMessage is treated as a trusted host boundary, but PixelAgentsViewProvider.onDidReceiveMessage dispatches an untrusted runtime object by message.type without a comprehensive schema validator.
- The terminal byte/frame WebSocket remains a separate data plane and is not part of the 34 ClientMessage / 46 ServerMessage control-plane count.

### Validation strength by message family

- Strong/shared parsers: saveCharacterProfiles parses profiles and wardrobe items; saveDirectory/removeDirectory use shared path/name checks; saveCardOrder parses IDs and filters them against existing agents; conversation limits/query/resume use dedicated normalization/validation helpers.
- Targeted checks: setUsageBridgeEnabled validates provider capability and enabled boolean; setBypassPermissions and setShowHostDirectories validate booleans; setHooksEnabled and hooksConsentResponse validate provider identity, while consentActionFor narrows the unknown choice.
- Partial or absent checks: saveLayout accepts a truthy value then casts; saveAreaMappings checks only non-null object on standalone and casts directly in the adapter; saveAgentSeats checks palette/repeat ranges only in standalone and the adapter passes seats directly to persistence; several settings cast booleans/strings; launchAgent casts directoryPath; external asset path checks are shallow.
- These are specific examples of the existing WV-C08 / F-08 validation gap, not new schema findings by themselves.

### Persistence and recipient patterns

- Shared broadcasts: agent lifecycle/activity, profile saves, card-order saves, directory-list mutations, bypass-permission posture, terminal lifecycle and privileged usage reports.
- Point-to-point replies/snapshots: handshake state, diagnostics, conversation results, directory acknowledgements/rejections/suggestions, hooks status/consent outcomes, and external-asset reload messages.
- Persist-only/no immediate peer update: area mappings, several settings toggles and seat metadata. Their cross-client semantics must be evaluated separately from transport delivery.
- saveLayout has a distinct watcher-based path; do not infer that every layout write is a store broadcast.

## 12. Newly identified static multi-client consistency gaps

### WV-PROTO-01 — Area mapping mutation is not propagated to other connected clients

- **Severity:** Medium. **Confidence:** High.
- **Status:** SOURCE-VERIFIED / STATIC-FINDING / OPEN. No multi-client runtime reproduction.
- **Affected files:** webview-ui/src/App.tsx; webview-ui/src/hooks/useDirectoryEditor.ts; webview-ui/src/hooks/useExtensionMessages.ts; webview-ui/src/office/engine/officeState.ts; server/src/clientMessageHandler.ts; adapters/vscode/PixelAgentsViewProvider.ts; server/src/httpServer.ts.
- **Evidence:** App.tsx:282–303 and useDirectoryEditor.ts:64–78 optimistically update only the sender's OfficeState and send saveAreaMappings. Standalone clientMessageHandler.ts:436–444 writes config.standalone.areaMappings and returns without store.broadcast or areaMappingsLoaded reply. The VS Code adapter PixelAgentsViewProvider.ts:635–639 writes config.vscode.areaMappings without sending a snapshot. The only areaMappingsLoaded consumer is useExtensionMessages.ts:779–783; the standalone handshake sends that snapshot at clientMessageHandler.ts:696–701. Meanwhile httpServer.ts:263–280 attaches each socket to the shared AgentStateStore agent/broadcast events, so a later agentCreated reaches every connected client, and OfficeState.findFreeSeat uses each client's local areaMappings at officeState.ts:368–395.
- **Technical explanation:** a second already-connected standalone client does not receive the first client's mapping update. On a subsequent shared agentCreated event, each client can run local seat selection against a different mapping snapshot.
- **Impact:** under multiple connected clients, the same new agent can be placed in different seats in each rendered office; the persisted mapping and reconnect snapshot eventually converge, but the live clients need not.
- **Remaining uncertainty:** multi-client seat-layout consistency has not been exercised at runtime; VS Code normally has one webview surface, so the strongest impact is standalone multi-tab/client use.

### WV-PROTO-02 — External asset directory reload is delivered only to the requesting client

- **Severity:** Low. **Confidence:** High.
- **Status:** SOURCE-VERIFIED / STATIC-FINDING / OPEN. No multi-client runtime reproduction.
- **Affected files:** webview-ui/src/components/SettingsModal.tsx; webview-ui/src/hooks/useExtensionMessages.ts; server/src/clientMessageHandler.ts; server/src/cli.ts; adapters/vscode/PixelAgentsViewProvider.ts.
- **Evidence:** standalone clientMessageHandler.ts:405–425 sends externalAssetDirectoriesUpdated using the current socket's send callback and invokes onReloadAssets(send). cli.ts:382–415 mutates the shared assetCache but sends characterSpritesLoaded, petSpritesLoaded and furnitureAssetsLoaded through that same requester-bound callback. useExtensionMessages.ts:872–875 updates only the receiving UI. The VS Code adapter similarly posts externalAssetDirectoriesUpdated and reloaded assets directly to its one webview at PixelAgentsViewProvider.ts:1008–1041.
- **Technical explanation:** other already-connected standalone clients are not sent the updated directory list or asset messages. A later webviewReady receives the updated shared cache, but an existing client can keep rendering the previous external asset catalog until it reconnects or reloads.
- **Impact:** stale character/pet/furniture visuals and settings state across concurrently open standalone clients after one client changes external asset directories.
- **Remaining uncertainty:** the intended live multi-client asset synchronization contract is not documented; no runtime reproduction was performed.

## 13. Verification limits

- All findings and mappings above are static source-derived. They do not establish that a browser/VS Code runtime reproduces the conditional multi-client effects.
- No tests, build, lint, typecheck, protocol generation, browser, VS Code host, multi-client WebSocket, PTY or reconnect experiment was executed.
- No source, test, package, configuration or asset file was modified.


## 14. GOV-WEBVIEW-011 — Field-level validation, authorization, persistence and side effects

**Status:** STATIC TRACE COMPLETE for the 34 ClientMessage variants. This is not runtime verification.  
**Source ref:** `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. The selected source files below were fetched from that ref; their blob SHAs are recorded in the session evidence log.  
**Contract:** `core/asyncapi.yaml` is the declarative source of truth. Its per-message schemas specify required fields/types, but no runtime validator is wired into either inbound host boundary. A TypeScript interface or AsyncAPI schema does not validate arbitrary JSON at runtime.

### 14.1 Per-message field and handling matrix

| ClientMessage | Contract fields | Runtime validation at ingress/handler | Authorization | Persistence / side effect / response |
|---|---|---|---|---|
| `webviewReady` | `type` | Discriminator only; no payload fields | Allowed to read-only clients | Sends handshake snapshots; may invoke external-agent restore and runtime ref synchronization; no user-config write |
| `launchAgent` | optional `directoryPath:string`, `palette:integer>=0` | Palette normalized through `rosterPick`; directoryPath is cast, not checked for string/existence | Privileged gate; standalone additionally requires runtime + PTY manager | Spawns PTY/process, creates agent, persists agent state, updates recent-character roster; no explicit launch ack |
| `focusAgent` | `id:integer` | No explicit type/range check; standalone server intentionally no-ops, UI handles drawer focus; VS Code Map lookup + terminal focus | Privileged gate on standalone | Focuses terminal only; no durable write |
| `closeAgent` | `id:integer` | No explicit type/range check; store lookup limits effect to an existing numeric key | Privileged gate | Disposes PTY / terminal or dismisses and removes observed agent; lifecycle broadcast via store events |
| `saveAgentSeats` | `seats:object<string,SeatAssignment>`; optional palette/repeat/seatId per item | Standalone checks only truthiness of top-level seats; palette/repeat ranges are checked only for agents lacking palette; nested item shape and seatId are not validated. A null item can throw during property access. Raw map is still passed to adapter persistence and conversation-look recording. VS Code persists raw `message.seats` directly. | Privileged gate | Persists per-agent seat metadata and session look; no explicit ack or broadcast. Partial in-memory mutations can precede a malformed later item throwing |
| `saveLayout` | `layout:object` | Standalone checks truthiness then casts; VS Code casts directly. No version/tile/nested-layout validation on this message | Privileged gate | Atomic JSON write helper catches/logs failure; standalone enables watcher reporting; VS Code watcher can notify other panels; no explicit success/rejection ack |
| `saveCharacterProfiles` | `profiles:array`, `saveId:string`, optional `items:array` | Shared parser validates profile and wardrobe-item structures; missing saveId is tolerated by helper for older clients despite schema required | Privileged gate | Writes wardrobe items then profiles via atomic per-file writes; updates caches/remaps agent looks/shared refs; success broadcast includes saveId; refusal/write failure replies to sender with current persisted/cache state |
| `setSoundEnabled` | `enabled:boolean` | No runtime boolean check in either host | Privileged gate | Per-host setting write; no broadcast or ack |
| `setLastSeenVersion` | `version:string` | Standalone/VS Code cast to string; no runtime check | Privileged gate | Per-host setting write; no broadcast or ack |
| `setAlwaysShowLabels` | `enabled:boolean` | No runtime boolean check | Privileged gate | Per-host setting write; no broadcast or ack |
| `setGhostHeadlessAgents` | `enabled:boolean` | No runtime boolean check | Privileged gate | Per-host setting write; no broadcast or ack |
| `setHooksEnabled` | `providerId:string`, `enabled:boolean` | Provider ID resolved against registry; enabled is cast/passed without boolean guard | Privileged gate; VS Code embedded webview trusted host boundary | Async install/uninstall; preference persisted only if re-read install state equals requested value; sends actual hooksStatus to requester; errors logged/reported by host |
| `hooksConsentResponse` | `providerId:string`, `choice:install\|notNow\|never` | Provider ID registry check; shared consent executor narrows exact choices and treats unknown choice as no-op | Privileged gate; consent request is not sent to read-only standalone client | May install/uninstall hooks, persist grant/decline/preference, update runtime ref; reports actual status; async |
| `setUsageBridgeEnabled` | `providerId:string`, `enabled:boolean` | Requires registered provider with usageBridge and boolean enabled | Privileged gate | Async provider settings/status-line modification; successful status broadcast; error status point-to-point |
| `setHooksInfoShown` | `type` only | No caller field; host hardcodes true | Privileged gate | Persists per-host setting; no ack |
| `setWatchAllSessions` | `enabled:boolean` | Cast only; no runtime boolean guard | Privileged gate | Persists setting and mutates runtime scanner ref; VS Code may dismiss/remove external agents when switched off; no ack/broadcast |
| `exportLayout` | `type` only | No caller fields | Standalone route absent; VS Code host action | Reads saved layout, opens native save dialog, writes user-selected file; no protocol ack |
| `importLayout` | `type` only | Native file JSON parse; validates only `version===1` and `tiles` is array, not nested tile schema | Standalone route absent; VS Code host action | Writes layout and sends layoutLoaded; dialog/parse errors shown natively |
| `exportFile` | `filename:string`, `dataBase64:string` | No complete runtime schema/size/base64 validation observed at message dispatch | VS Code host action; standalone route absent (browser downloads locally) | Opens native save dialog and writes decoded file data to user-selected destination |
| `openSessionsFolder` | `type` only | No caller fields | VS Code host action; standalone route absent | Opens known sessions folder if it exists; no durable config write |
| `addExternalAssetDirectory` | optional `path:string` (standalone); omitted for VS Code picker | Standalone only checks truthiness, not string/path existence/directory; VS Code obtains folder through native picker | Privileged gate on standalone; embedded host action | Mutates shared config, reloads assets, sends directory/assets messages to requester. `writeConfig` result is ignored |
| `removeExternalAssetDirectory` | `path:string` | Standalone only checks truthiness; no string/path shape guard | Privileged gate / VS Code host action | Filters shared config, reloads assets, sends requester-only messages. `writeConfig` result is ignored |
| `saveAreaMappings` | `mappings:object<string,string[]>` | Standalone checks non-null object only (arrays pass); adapter casts directly. No validation of each key/value/area label | Privileged gate | Writes host-namespaced areaMappings; no ack or broadcast; sender optimistic update only. See WV-PROTO-01 |
| `saveCardOrder` | `order:integer[]` | Shared parser requires array, filters safe non-negative integers, deduplicates and caps length; then filters to existing agent IDs | Privileged gate | Persists through StateAdapter; broadcasts canonical cardOrderLoaded if persistence adapter exists |
| `setShowAreas` | `enabled:boolean` | Cast only; no runtime boolean guard | Privileged gate | Per-host setting write; no ack/broadcast |
| `setBypassPermissions` | `enabled:boolean` | Explicit boolean guard and adapter existence check | Privileged gate | Persists per-host launch permission posture; broadcasts read-back value to all clients |
| `setShowHostDirectories` | `enabled:boolean` | Explicit boolean guard and adapter existence check | Privileged gate | Persists per-host setting; broadcasts refreshed directories list |
| `saveDirectory` | `name:string`, `path:string`; optional previousPath, characters, characterOrder | Type-narrows name/path/previousPath; expands tilde, requires existing directory, normalizes/deduplicates paths; parses character defaults/order | Privileged gate | Shared machine-wide config write; checks writeConfig result; success broadcasts directoriesLoaded then sender-only directorySaved; failure sender-only directoryRejected |
| `removeDirectory` | `path:string` | Requires string; normalized path; removal is bookkeeping only | Privileged gate | Shared config write checked; success broadcasts directoriesLoaded; failure replies directoryRejected |
| `requestDirectorySuggestions` | `type` only | No caller fields | Privileged gate | Filesystem/transcript-derived suggestions; requester-only response; no persistence |
| `requestConversations` | optional `limits:ConversationLimit[]` | Shared helper normalizes/clamps requested limits; no mutation | Privileged gate and standalone PTY/provider availability | Reads transcript/session metadata; requester-only conversationsLoaded |
| `searchConversations` | `query:string`, optional directoryPath, `limit:integer` | Shared query helper validates/normalizes query and limit; no mutation | Privileged gate and standalone PTY/provider availability | Searches transcript titles; requester-only conversationSearchResults |
| `resumeConversation` | `sessionId:string`, `directoryPath:string` | UUID pattern; string directory; provider capability; rejects already-running session and missing transcript | Privileged gate and standalone runtime + PTY/provider availability | Launches/resumes PTY using transcript and remembered look; no explicit result ack |
| `requestDiagnostics` | `type` only | No caller fields | Privileged gate | Reads current agent diagnostics; requester-only response |

### 14.2 Authorization model

- Standalone authorization is connection-scoped: the HTTP/WebSocket handshake determines `privileged`; `handleClientMessage` refuses every message except `webviewReady` when false. This is fail-closed for newly added message types. The UI's `officeAccess` flag is presentation, not enforcement.
- The gate is not field-level authorization. A privileged token grants the whole command surface, including filesystem directory selection, process launch/close, provider settings mutation, layout/config persistence and diagnostics. No per-directory ownership or per-message capability scopes were observed.
- VS Code webview messages are dispatched directly by `onDidReceiveMessage`; no parallel token gate or comprehensive runtime schema validator exists there. This is an extension-host trust boundary, not proof that the payload is structurally valid.
- Usage snapshots/reports are separately withheld from read-only clients. Terminal raw-frame WebSocket auth remains a distinct data plane.

### 14.3 Persistence and side-effect conclusions

- **Durable, shared machine-wide:** character profiles/wardrobe items, external asset directory list, user Directories, hook consent/preferences, recent character roster, conversation looks, layout file. **Host-namespaced:** settings and area mappings. **Adapter/session state:** agent seats and card order. **Runtime-only:** scanner refs, focus, current agent activity, UI state.
- Strongest input boundaries are shared profile/wardrobe parsers, directory path validation, card-order normalization, conversation query/UUID validation, provider-ID lookup, strict boolean guards on bypass/show-host/usage-bridge and consent executor's exact enum handling.
- The weakest boundaries are saveAgentSeats, saveLayout/importLayout nested data, saveAreaMappings, general setting toggles, setHooksEnabled.enabled and external asset directory paths. The contract schema is stricter than the actual handler in these cases.
- Several mutation paths are fire-and-forget or do not confirm durable write success. In particular external asset directory add/remove ignore writeConfig's boolean result but send externalAssetDirectoriesUpdated and initiate asset reload. Settings/layout/area/seat writes generally have no explicit negative acknowledgement; the UI can remain optimistic until a later handshake or snapshot.
- Character profile save uses atomic writes per file, not a transaction spanning wardrobe-items.json and characters.json. The items file/cache can be updated before a later profiles write fails; refusal is therefore not guaranteed to roll back the first file.
- saveAreaMappings and external asset reload remain distinct from normal store broadcasts; see WV-PROTO-01/02.

## 15. Extended validation finding and new persistence finding

### WV-C08 / F-08 — Runtime validation gap (extended evidence; existing canonical finding)

Do not create a duplicate finding. GOV-WEBVIEW-011 extends the existing cross-layer finding with these concrete cases:

1. `saveAgentSeats`: standalone only partially validates palette/repeat before still persisting the raw object; VS Code persists the raw object directly. Nested null/primitive values and malformed seatId are not rejected as a whole.
2. `saveAreaMappings`: standalone accepts any non-null object (including arrays) and casts nested values; VS Code casts directly. `parseAreaMappings` is a read-time coercion, not ingress validation.
3. `saveLayout` and VS Code import: opaque object is accepted without validating layout/tile structure; import only checks version and tiles-array existence.
4. General settings toggles and `setHooksEnabled.enabled` do not enforce the declared boolean/string field types at runtime.
5. `addExternalAssetDirectory` / `removeExternalAssetDirectory` use truthiness rather than string/path validation in standalone.

**Impact:** malformed privileged payloads can cause partial state mutation, malformed persisted values, ignored settings, or a client/server state divergence. No unauthenticated write is implied: standalone mutation still requires a privileged connection.  
**Verification:** SOURCE-VERIFIED / STATIC-FINDING; not test-executed or runtime-reproduced.  
**Remaining uncertainty:** exact behavior of malformed values depends on StateAdapter implementation, filesystem permissions and subsequent config normalization; no malicious-client experiment was run.

### WV-PROTO-03 — External asset directory changes report success after a failed config write

- **Severity:** Low. **Confidence:** High. **State:** OPEN.
- **Affected files:** `server/src/clientMessageHandler.ts`; `adapters/vscode/PixelAgentsViewProvider.ts`; `server/src/configPersistence.ts`.
- **Evidence:** standalone add/remove mutate the object returned by readConfig, call writeConfig, then send externalAssetDirectoriesUpdated and invoke onReloadAssets without checking its boolean result. VS Code add/remove follows the same write-then-reload/report pattern. writeConfig explicitly returns false when the atomic write fails.
- **Technical explanation:** the response is constructed from the in-memory candidate config, not a verified read-back. If persistence fails, the client may display the candidate directory list and receive reload messages even though a subsequent read/handshake returns the prior on-disk list.
- **Impact:** transient false success and UI/assets state divergence after filesystem/config write failure.
- **Verification:** SOURCE-VERIFIED / STATIC-FINDING; no write-failure fault injection.
- **Remaining uncertainty:** the precise UI symptom depends on the reload callback and timing; no runtime failure was induced.

## 16. Verification boundary and next step

- 34/34 ClientMessage variants have field, validation, authorization, persistence/side-effect and response behavior statically classified.
- The 34/34 and 46/46 producer-consumer mapping from GOV-WEBVIEW-010 is reused; it was not repeated.
- Selected source blobs are reconciled against the documented `main@56f354f` baseline. A fresh recursive tree was not available through the repository connector in this session, so current whole-repository TREE-VERIFIED status is inherited as DOC-RECORDED from GOV-WEBVIEW-008 rather than newly claimed.
- No tests, builds, lint, typecheck, protocol generation, browser/VS Code runtime, write-failure injection or multi-client experiment was executed. No application source was modified.
- Next logical audit: lifecycle/reconnect and cross-client state reconciliation; runtime experiments remain gated on explicit authorization.

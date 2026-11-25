# Core Protocol

**Status:** AUDITED — source-of-truth and generation pipeline inspected.

## 1. Source of truth

- Specification: `core/asyncapi.yaml`
- AsyncAPI version: `3.0.0`
- Protocol metadata version: `1.0.0`
- Channel: `ws`, address `/ws`
- Server definition: local `ws://127.0.0.1:3100/ws`
- Generated TypeScript: `core/src/messages.ts`
- Generator: `scripts/generate-messages.ts`
- Validator: `scripts/validate-asyncapi.mjs`

The YAML is authoritative. Never hand-edit `messages.ts`; update the YAML and regenerate.

## 2. Direction and message families

The channel is bidirectional:
- Server sends the `ServerMessage` union to clients.
- Client sends the `ClientMessage` union to server.

### Server → client families

| Family | Message types / purpose |
|---|---|
| Provider capabilities | Registered provider capability information |
| Agent lifecycle | Created, closed, selected, existing-agent snapshot |
| Agent activity | Status, tool start/done/clear, permission indicators |
| Subagent activity | Subagent tool start/done/clear and permission |
| Teams | Agent team metadata |
| Context usage | Agent context-window usage |
| Layout | Office layout snapshot |
| Visual assets | Furniture catalog, character profiles/recency/sprites, pet sprites, floor/wall/carpet tiles |
| Settings and consent | Office access, settings, bypass permission, hooks status/consent |
| Usage | Usage bridge status and account usage limits |
| Directories and conversations | External asset directories, area mappings, card order, directories, save/reject, suggestions, recent/search results |
| Terminal control plane | Terminal availability and terminal session opened/closed |
| Diagnostics | Agent diagnostics |

### Client → server families

| Family | Message types / purpose |
|---|---|
| Lifecycle and launch | WebviewReady, LaunchAgent, FocusAgent, CloseAgent |
| Persisted UI/domain state | SaveAgentSeats, SaveLayout, SaveCharacterProfiles, SaveAreaMappings, SaveCardOrder |
| Preferences | Sound, last-seen version, labels, ghost headless agents, bypass permissions, show areas/host directories, watch all sessions |
| Provider hooks/usage consent | SetHooksEnabled, HooksConsentResponse, SetUsageBridgeEnabled, SetHooksInfoShown |
| Layout/files | ExportLayout, ImportLayout, ExportFile, OpenSessionsFolder |
| Assets | AddExternalAssetDirectory, RemoveExternalAssetDirectory |
| Directory/conversation | SaveDirectory, RemoveDirectory, RequestDirectorySuggestions, RequestConversations, SearchConversations, ResumeConversation |
| Diagnostics | RequestDiagnostics |

The full field-level schema is in `asyncapi.yaml`; this overview is intentionally not a duplicate of every payload definition.

## 3. Generated TypeScript contract

`messages.ts` exports the top-level `ServerMessage` and `ClientMessage` discriminated unions and named payload types. Consumers narrow by the `type` literal. The generator customizes Modelina so reserved keys such as `type` and `status` remain unchanged and `const` schemas become inline literal types. It also removes generated dead anonymous single-value enum aliases and the artificial `additionalProperties` field for open schemas.

## 4. Generation and validation

- Generate: `npm run asyncapi:generate` → `tsx scripts/generate-messages.ts`
- Validate: `npm run asyncapi:validate` → `node scripts/validate-asyncapi.mjs`
- Root `compile` and `package` scripts generate messages before type-check/build.
- Generator writes `core/src/messages.ts`, then runs Prettier and ESLint fix.
- Validator parses YAML using `@asyncapi/parser`; severity-error diagnostics or missing parsed document fail the command.

The YAML explicitly pins 3.0.0 because the installed Modelina 5.10.1 generation path is documented in the spec as supporting 3.0.0; a naïve bump to 3.1.0 can produce invalid/incomplete generated output. Treat this as a toolchain compatibility constraint.

## 5. Protocol versioning and compatibility

- AsyncAPI `info.version: 1.0.0` is the protocol metadata version; do not confuse it with the AsyncAPI document version.
- Provider hook protocol has a separate `HookProvider.protocolVersion` contract in `provider.ts`. The server rejects unsupported provider protocol versions, per its interface documentation.
- Message schemas generally use a `type` discriminator and explicit required fields / `additionalProperties: false` where appropriate.
- Compatibility rules are field-specific and described inline in schema documentation. The spec includes migration-friendly/open profile payloads where older profile formats must reach the authoritative validator/migrator.
- No universal negotiated version handshake for the UI message protocol was identified in the inspected channel definition. **OPEN:** confirm any compatibility enforcement elsewhere in server/client bootstrap before making a claim about runtime negotiation.

## 6. Separate terminal data plane

Terminal control facts (availability/open/close) are AsyncAPI messages. High-frequency raw terminal data is a separate WebSocket stream with frames defined by `core/src/terminalFrames.ts`:
- server → client: replay, output, exit
- client → server: input, resize

This separation is deliberate: raw terminal byte/data flow is not modeled as ordinary control-plane events. See `docs/design/standalone-terminal.md` and `core/src/constants.ts` for route prefix.

## 7. Security boundary

AsyncAPI schemas describe structure, not authorization. Every inbound message still requires server-side authorization, validation and state checks. Likewise terminal frame parsers provide structural validation only; they do not authenticate a peer or authorize access to an agent's terminal.

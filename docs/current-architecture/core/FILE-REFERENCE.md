# Core File Reference

**Status:** AUDITED — all 26 tracked files under `core/` inventoried from the recursive Git tree and inspected at source level. Generated source is explicitly marked. The repository contains 1 YAML specification and 25 TypeScript files.

## Protocol and package surface

### `core/asyncapi.yaml`
- **Status:** VERIFIED — protocol source of truth.
- **Responsibility:** AsyncAPI 3.0 specification for the Pixel Agents bidirectional UI/server WebSocket control protocol.
- **Components:** ServerMessage and ClientMessage discriminated unions; named payload schemas; `/ws` channel and send/receive operations.
- **Relationships:** Generates `core/src/messages.ts` via `scripts/generate-messages.ts`; validated by `scripts/validate-asyncapi.mjs`.
- **Important boundary:** Terminal raw I/O frames are intentionally outside this control-plane schema.
- **Compatibility note:** File pins AsyncAPI 3.0.0 because Modelina 5.10.1 generator support is documented as limited to 3.0.0. Do not casually bump to 3.1.0 without upgrading/validating generation.

### `core/src/index.ts`
- **Status:** VERIFIED — public barrel.
- **Responsibility:** Re-exports selected interfaces, types and constants for cross-layer consumers.
- **Exports:** StateAdapter; shared path/display constants; ClientMessage/ServerMessage; AgentEvent/HookProvider; shared schemas; TeamProvider.
- **Relationships:** Intended import surface for consumers that need the curated core API.
- **Caveat:** Header comment claims all package contents are types-only, which conflicts with runtime exports and executable modules in this folder. It also does not re-export every runtime utility or asset function.

### `core/src/messages.ts`
- **Status:** VERIFIED — generated; DO NOT EDIT manually.
- **Responsibility:** TypeScript declarations generated from AsyncAPI schemas.
- **Components:** ClientMessage/ServerMessage unions and their constituent message/payload types.
- **Relationships:** Imported by server and UI message handling and by other core contracts. Regenerate with `npm run asyncapi:generate`.
- **Invariant:** YAML is source of truth; changes belong in `asyncapi.yaml`, then generation and diff review.

## Shared contracts and domain shapes

### `core/src/adapter.ts`
- **Responsibility:** `StateAdapter` interface for load/save of persisted agents, seats, optional card order, and generic settings.
- **Relationships:** Host/server persistence implementations satisfy it; agent state shape comes from `schemas.ts`.
- **Side effects:** Interface only; implementations own storage and I/O.
- **Important boundary:** Layout persistence is explicitly outside this adapter.

### `core/src/provider.ts`
- **Responsibility:** Provider-neutral event and capability contracts.
- **Components:** `AgentEvent` union; `ReportedUsage`, `ReportedContext`, `UsageBridge`, `HookProvider`.
- **Relationships:** Concrete CLI provider implementations normalize raw hooks into AgentEvent; server handlers consume normalized events; optional team behavior delegates to TeamProvider; usage reporting feeds usage endpoints/state.
- **Caveat:** Current primary abstraction is hook-based. Transcript polling/push providers are explicitly future TODOs, not implemented abstractions.
- **Domain warning:** AgentEvent describes observed external coding-agent activity, not future internal LLM worker tasks.

### `core/src/teamProvider.ts`
- **Responsibility:** Optional semantic interface for CLIs supporting lead/teammate teams.
- **Components:** SpawnNotice, teammate spawn classification, discovery, transcript/team metadata extraction, membership queries and background-spawn correlation.
- **Relationships:** Optional `HookProvider.team`; server team-aware handlers/adoption/transcript logic call provider semantics.
- **Side effects:** Interface only; provider implementation performs discovery/I/O.
- **Caveat:** Claude Agent Teams is the concrete current design reference; other providers are hypothetical until implemented.

### `core/src/schemas.ts`
- **Responsibility:** Shared persisted/wire shapes.
- **Components:** PersistedAgent, AgentMeta, OfficeLayout, PlacedFurniture, ColorValue, FloorColor, SpriteData, FurnitureCatalogEntry, HookEvent, Disposable.
- **Relationships:** StateAdapter, server persistence and protocol/asset flows; some types are intentionally public even if consumers keep local mirrors.
- **Caveat:** PersistedAgent contains compatibility fields retained for reading old persisted state; comments say old fields are not written.

### `core/src/terminalAdapter.ts`
- **Responsibility:** Minimal host terminal discovery interface (`TerminalHandle`, `ITerminalAdapter`).
- **Relationships:** File/session adoption logic can query active/all terminals without importing VS Code APIs. VS Code adapter implements it; standalone can expose no terminals.
- **Side effects:** None in core.

### `core/src/transport.ts`
- **Responsibility:** `MessageTransport` contract and `TransportState` union.
- **Components:** send ClientMessage, subscribe to ServerMessage, first-ready promise, connection-state subscriptions and disposal.
- **Relationships:** PostMessage and WebSocket transport implementations live in webview; core constants define state strings.
- **Side effects:** Interface only.

### `core/src/constants.ts`
- **Responsibility:** Cross-layer constants for hook/usage API prefixes, server metadata filenames, display limits, terminal WebSocket prefix and transport lifecycle states.
- **Relationships:** Used by server, adapter and webview; server-specific timing and UI rendering constants are intentionally elsewhere.
- **Side effects:** None.

### `core/src/terminalFrames.ts`
- **Responsibility:** Encode/decode and structurally validate terminal WebSocket frame JSON.
- **Components:** TerminalServerFrame (replay/output/exit), TerminalClientFrame (input/resize), encodeTerminalFrame, parseClientFrame, parseServerFrame.
- **Relationships:** Shared by server terminal route and browser terminal client; complements AsyncAPI control-plane terminal availability/open/close messages.
- **Validation:** Invalid JSON, unknown types, wrong field types and non-positive/non-integer geometry are rejected with null.
- **Boundary:** Raw terminal data plane, deliberately outside AsyncAPI.

## Character and identity domain

### `core/src/characterProfiles.ts`
- **Responsibility:** Character profile contract refinement, validation, bounded parsing, migration and stable identity remapping.
- **Components:** CHARACTER_SLOTS, EYE_ROWS, CharacterProfile, CHARACTER_PROFILE_VERSION (35), MAX_CHARACTER_PROFILES (500), pixId/parsePixId, newCharacterId, isCharacterProfile, parseCharacterProfiles, characterRemap, migrateCharacterProfile.
- **Relationships:** Uses generated profile/color/edit types from messages.ts; sheet constants and legacy migration from assets; isRecord/localId helpers; wardrobe editor/webview and server persistence consume it.
- **Important invariant:** Profile index is palette index, so stable IDs and characterRemap preserve references when list order changes.
- **Compatibility:** Migration handles historical body/eye/part renames, retired parts, edit keys and legacy 7-frame sheets.

### `core/src/defaultCharacterProfiles.ts`
- **Responsibility:** Defines the six built-in starter character profiles and assigns stable `char-N` IDs/current profile version.
- **Relationships:** Supplies initial cast defaults to server/webview flows; conforms to CharacterProfile.
- **Side effects:** None.

### `core/src/directoryCharacters.ts`
- **Responsibility:** Parse and choose Directory default character indices.
- **Components:** MAX_DIRECTORY_CHARACTERS (64), parseDirectoryCharacters, parseCharacterOrder, pickDirectoryCharacter.
- **Relationships:** Used by palette selection and server Directory launch logic; values are palette indices.
- **Validation:** Drops invalid/negative/non-integer/duplicate entries and caps count; only exact `random` selects random ordering.

### `core/src/paletteUtils.ts`
- **Responsibility:** Select visual palette and repeat for a newly launched agent.
- **Components:** Look/WornLook, repeatFor, pickRandomPalette, rosterPick, LookRequest, chooseLook.
- **Relationships:** Uses directoryCharacters; server launch flow assigns persisted palette/repeat; webview uses them to render identity.
- **Invariant:** Roster choice takes precedence, then free Directory default, then balanced random choice; repeat index prevents identical copies from sharing the same visual variant.

### `core/src/wardrobeItems.ts`
- **Responsibility:** Validate bounded custom wardrobe item payloads shared across client/server.
- **Components:** ItemPiece, ITEM_PIECES, MAX_WARDROBE_ITEMS (200), MAX_WARDROBE_ITEM_PNG_CHARS, ITEM_ID_RE, isItemPiece, isItemId, parseWardrobeItems.
- **Relationships:** Uses CHARACTER_SLOTS and generated WardrobeItem schema; server validates before persistence and webview validates before decoding.
- **Security/invariants:** Rejects unknown keys, invalid slot/id/base, blank/oversized labels, invalid/oversized base64 and duplicate IDs. PNG semantic validity remains the decoder/UI's responsibility.

### `core/src/isRecord.ts`
- **Responsibility:** Type guard for non-null, non-array JavaScript objects.
- **Relationships:** Shared validation helper used by profile/wardrobe parsing.
- **Side effects:** None.

### `core/src/localId.ts`
- **Responsibility:** Generate local non-cryptographic IDs from prefix, base-36 timestamp and Math.random suffix.
- **Relationships:** Used by character profile ID generation and other local identity needs.
- **Security note:** Explicitly not a cryptographic token/secret generator; comments cite compatibility with plain-HTTP LAN contexts where crypto.randomUUID may be unavailable.

### `core/src/normalizeProjectPath.ts`
- **Responsibility:** Normalize absolute project paths into deterministic Claude-style project directory names by replacing non-alphanumeric/non-hyphen characters with hyphens.
- **Relationships:** Server session/project discovery and persistence path mapping.
- **Caveat:** This is a naming transform, not a canonical path/security validation function.

## Asset pipeline

### `core/src/assets/types.ts`
- **Responsibility:** Internal asset pipeline structures: CharacterDirectionSprites, AssetIndex, CatalogEntry, PetSpriteFrames, PetManifest.
- **Relationships:** Used by asset builders/loaders/decoders and server/webview asset flows.
- **Side effects:** Types only.

### `core/src/assets/constants.ts`
- **Responsibility:** Dimensions and parsing constants for PNG alpha, floors, walls, character sheets, pets and carpets.
- **Relationships:** Used by PNG decoder, character sheet conversion and seated-frame migration.
- **Side effects:** None.

### `core/src/assets/colorUtils.ts`
- **Responsibility:** Convert RGBA pixels into normalized hex strings; alpha below threshold becomes empty/transparent.
- **Relationships:** Used by PNG decoders and character sheet conversion.
- **Side effects:** None.

### `core/src/assets/characterSheet.ts`
- **Responsibility:** Convert RGBA character sheet data into direction/frame/pixel grids and widen legacy 7-frame sheets to current 8-frame layout.
- **Relationships:** Uses colorUtils, asset constants and seatedFrame; consumed by PNG decoder.
- **Invariant:** Three direction rows (down/up/right), eight frames per row, 16×32 each; left direction is derived by renderer.

### `core/src/assets/seatedFrame.ts`
- **Responsibility:** Derive seated idle frame from typing frames and migrate legacy sprite/edit layouts from seven to eight frames per row.
- **Relationships:** Used by characterSheet and characterProfiles migration.
- **Compatibility:** Shared derivation keeps base art and pixel edits aligned.

### `core/src/assets/pngDecoder.ts`
- **Responsibility:** Decode PNG buffers into pixel-grid data for generic sprites, walls, carpets, characters, floors and pets.
- **Relationships:** Uses pngjs, characterSheet, colorUtils, asset constants and asset types; called by asset loader and other asset consumers.
- **Error behavior:** Generic sprite decoder returns transparent fallback on parse failure; pet decoder returns correctly shaped empty frames; wall/carpet/character decoding can propagate decoder errors to caller.
- **Side effects:** CPU/memory allocation and console warnings; no filesystem access.

### `core/src/assets/manifestUtils.ts`
- **Responsibility:** Define furniture manifest node types and recursively flatten nested rotation/state/animation groups into leaf furniture entries with inherited metadata.
- **Relationships:** Used by buildFurnitureCatalog; resulting catalog consumed by asset loaders and UI.
- **Side effects:** Pure transformation.

### `core/src/assets/build.ts`
- **Responsibility:** Build furniture catalog from asset-directory manifests and create sorted asset index (floors, walls, latest default layout).
- **Relationships:** Uses manifestUtils/types; build tooling and host asset packaging consume outputs.
- **Side effects:** Filesystem reads; malformed/missing manifests are skipped.

### `core/src/assets/loader.ts`
- **Responsibility:** Read floor/wall/carpet/furniture PNGs from an assets directory and decode them.
- **Relationships:** Uses pngDecoder and CatalogEntry; server asset loading uses decoded data for client broadcasts.
- **Side effects:** Filesystem reads; malformed furniture images are skipped with warnings.

## Tests and verification

- Core does not have a dedicated `core/test` directory in the audited tree.
- Root scripts run type-checking and lint over core; server and webview test suites may exercise core functions indirectly.
- **OPEN:** Exact test-to-core-function coverage has not yet been mapped. Do not claim every core function has a direct unit test.
- **OPEN:** Build/test commands were not run during this documentation audit.

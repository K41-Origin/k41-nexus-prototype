# Audit Evidence Log

Append-only session ledger. Corrections must be appended; do not erase historical entries.

## 2026-11-25 — GOV-CORE-001: core source and documentation re-verification

**Repository:** `K41-Origin/k41-nexus-prototype`  
**Branch inspected:** `docs/hybrid-llm-engine-direction`  
**Branch tip inspected:** `13354f1a74b75b9ab3d8982544414134b1940ae3`  
**Historical core inventory root:** `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1`  
**Scope:** all tracked files under `core/` and all four core architecture documents.  
**Method:** recursive tree inventory + individual file retrieval + static cross-check of source and docs.

### Verified inventory

- Recursive tree: `truncated: false`.
- Total: **28 tracked files** = 1 YAML AsyncAPI specification + 27 TypeScript files.
- All 28 core blob SHAs at the branch tip match the historical core audit snapshot.
- Four core documents were retrieved and reviewed: README, MODULE-OVERVIEW, FILE-REFERENCE, PROTOCOL, DEPENDENCIES (five documents including README).
- All 27 TypeScript files and `core/asyncapi.yaml` were individually fetched. Large AsyncAPI spec was retrieved in line ranges.
- Core source was not modified.

### Verification limitations

- Static source/document review only.
- No install, test, typecheck, lint, build, AsyncAPI validation/generation, package inspection, browser/server runtime, or cross-host runtime trace was executed in this session.
- Consumer import claims that the core docs explicitly mark as inferred/open remain open.
- No claim is made that every contract has direct unit-test coverage.

### Corrections / confirmations recorded

1. **Confirmed:** core is not types-only. It contains runtime pure utilities and Node-specific filesystem/PNG asset tooling; `core/src/index.ts` header comment is inaccurate relative to its exports and package contents.
2. **Confirmed:** AsyncAPI YAML is the source of truth for generated `core/src/messages.ts`; generated file must not be edited manually.
3. **Confirmed:** raw terminal frames are a separate data plane from the AsyncAPI WebSocket control plane.
4. **Confirmed:** no upward imports from `server/`, `webview-ui/`, or `adapters/` are reported by the existing static core source audit. This is a source-import boundary, not proof of full package isolation.
5. **Confirmed as OPEN packaging risk:** `pngjs` is imported at runtime by `pngDecoder.ts` but root manifest classifies it as a devDependency. Bundle/package reachability must be checked before calling this a defect.
6. **Confirmed as OPEN:** exact public export consumption, duplicate/mirrored consumer types, architecture enforcement and full consumer graph need layer-level tracing.
7. **Confirmed:** binary/runtime and malformed-input behavior is not fully proven by static inspection alone.

### Core source blob SHA manifest

| Path | Blob SHA |
|---|---|
| `core/asyncapi.yaml` | `ff1247118819fe68852da11996c660df1726aa66` |
| `core/src/adapter.ts` | `af34de4cd31eaa0abff4298968b36c5a97de701f` |
| `core/src/assets/build.ts` | `08d04b03d8937558c437dc3de7a9dab06568aad3` |
| `core/src/assets/characterSheet.ts` | `d8070f7231d2bc26e3d7ae4ac061c44d0be0fb92` |
| `core/src/assets/colorUtils.ts` | `e65673cb8041c4097a1d17d013c7a2a04556dc9c` |
| `core/src/assets/constants.ts` | `600dfac29faca1b0de9a81d0901806e0454cf11e` |
| `core/src/assets/loader.ts` | `0cbd192c01398fa1f62fcdafd67f85a0882a1266` |
| `core/src/assets/manifestUtils.ts` | `236256dd55286644aa27be53194f65b199ae6828` |
| `core/src/assets/pngDecoder.ts` | `4a912de0553b57d71476e745fb4c4d4ce3553d3d` |
| `core/src/assets/seatedFrame.ts` | `dc6ee02154df275d64e18cf192821b1079a116e3` |
| `core/src/assets/types.ts` | `ca0391111e30954ca23703a432c565a4593a0289` |
| `core/src/characterProfiles.ts` | `cff5dd116b691471b054b9a74eac44fe51f3c68e` |
| `core/src/constants.ts` | `4da3ce4c4bfe12a95324fe8eb4fe468d4cf60ef7` |
| `core/src/defaultCharacterProfiles.ts` | `fefcf91d1656e563d4a5fdf81314f002616a5d0f` |
| `core/src/directoryCharacters.ts` | `70d6c30250a2272659d5bfb0cc28677828165379` |
| `core/src/index.ts` | `efc8aa0a5a7cf56b6a5ef939ac651e68bf2ff84d` |
| `core/src/isRecord.ts` | `c4cd1e8ac3180ed62b00350735efba16b693879c` |
| `core/src/localId.ts` | `f66ecf00483702c24ed76d8239fc61e654923d1f` |
| `core/src/messages.ts` | `2fd177f12327c03ae5bdbc4beb83360121c5f703` |
| `core/src/normalizeProjectPath.ts` | `cfb52f58238dccf21989c2dae2764ecfe30c4565` |
| `core/src/paletteUtils.ts` | `40b5c98d3bcd818e3355b827af969d55a4110bce` |
| `core/src/provider.ts` | `03c25878187aeeea8e710b1abde82c7efaab5ec9` |
| `core/src/schemas.ts` | `2a9a364da35a5f95bed8768cfb08f87a2b8e1942` |
| `core/src/teamProvider.ts` | `ec694d694068f47677b1052d4c1d747becbe8b2d` |
| `core/src/terminalAdapter.ts` | `f153ceeb6fad030d7287fc3bcaf41243e38797f9` |
| `core/src/terminalFrames.ts` | `e91f11eac94dac7f1c3f27950c5006e8fee5086c` |
| `core/src/transport.ts` | `1af71a0ed5bd0d4354680ea6f3fc7fa3b19ea168` |
| `core/src/wardrobeItems.ts` | `8ba819f78037b1a1b3bf0e0bce0073173e673ad4` |

### Core documentation blob SHAs

| Path | Blob SHA |
|---|---|
| `docs/current-architecture/core/README.md` | `074b4e618689f128ad233aeb39c8aa4c156743b0` |
| `docs/current-architecture/core/MODULE-OVERVIEW.md` | `2c940982b5835da6f459ebcd0e3f662318301fb5` |
| `docs/current-architecture/core/FILE-REFERENCE.md` | `42a8296499345a0cfe61c376236a617c7b782293` |
| `docs/current-architecture/core/PROTOCOL.md` | `159427ef825668a0c4d43c466716bb2d94031ec6` |
| `docs/current-architecture/core/DEPENDENCIES.md` | `d27fcf9ba861fe2fc8ec203ec9a2877e594635df` |

### Continuation pointer

Next module work remains governed by the register. For the active deep audit, resume `webview-ui/` at reconnect/snapshot resynchronization in `useExtensionMessages.ts`, then continue only the recorded evidence gaps. Do not reopen completed `core/` source review unless a blob SHA changes or a concrete cross-module question requires it.

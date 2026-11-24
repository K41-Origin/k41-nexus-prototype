# Rendering Engine

**Status:** STATIC RENDERING MAP — performance/lifecycle verification remains open.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Rendering architecture

The office is a 2D pixel-art scene using Canvas 2D. It is not a WebGL/Three.js renderer. React owns the UI shell and overlays; the office scene renderer draws the world canvas using the in-memory OfficeState and sprite/catalog data.

Principal path:
OfficeCanvas -> OfficeState / editor render state -> office renderer -> tile/furniture/character/pet sprite data -> Canvas 2D.

The game loop drives animation/update work separately from React component rendering. Tool overlays and selected interaction UI may be layered over the canvas in DOM.

## 2. Scene inputs

- OfficeState: agents, seats, tile map, furniture placement, pet entities and selection state.
- Layout modules: floor/wall/furniture definitions, serialized layout and workstations.
- Asset stores: floor tiles, wall tiles, carpet tile sets, furniture catalog/sprites, pet templates and external character sprites.
- Agent activity: status, tool activity, permission waits, team/subagent state and provider capabilities.
- Editor state: active tool, selection, hover/cursor, placement previews and editor render state.

## 3. Character rendering

Character studio rendering is a distinct subsystem:
- wardrobe.json defines part categories and palettes.
- loadWardrobe.ts eagerly imports/inlines PNG art and decodes the wardrobe.
- characters/renderer.ts composes profile layers into sprite sheets and maintains caches.
- SpriteCanvas/Stage/PixelEditor draw sheets and edit individual frame pixels.
- Pixel edits are stored as uncoloured edits associated with a piece/frame so they follow the part's color treatment.

The 81 built-in character PNGs have valid PNG signatures and 128×96 IHDR dimensions. This check does not validate complete pixel data/CRC.

## 4. Asset loading

Production:
server assetLoader -> core PNG decoding/build pipeline -> LoadedAssets messages -> useExtensionMessages -> furniture catalog, floor/wall/carpet/pet/character stores.

Vite development:
Vite plugin exposes decoded asset JSON endpoints; browserMock injects equivalent messages through WebSocketTransport.deliver(). The development mock is not the production asset loader.

The 25 public furniture manifests and their sprite assets were reviewed. Public asset dimensions are recorded in ASSET-INVENTORY.md.

## 5. Cache and lifecycle findings

- WV-O04: furniture catalog, sprite/colorization/carpet caches use module-global mutable state with different reset/bound behavior.
- WV-ASSET-03: empty dynamic catalog rebuild can return before replacing/clearing previous module-global catalog state.
- WV-ASSET-04: colorized carpet cache has no visible capacity/eviction bound.
- WV-C10: character renderer caches have differing bounds; beard cache growth remains to be assessed.
- WV-WARD-01: rejected wardrobe load Promise remains memoized and blocks retry.
- WV-C02: LoadingScene canvas transformation cache lifecycle needs bounds/disposal analysis.
- WV-O05: ToolOverlay may trigger React state updates per animation frame.
- WV-C01: IntroBubble may trigger React state updates per animation frame.
- WV-O06: OfficeCanvas DPR changes may not be detected when CSS dimensions remain constant.

These are static observations. Memory growth, frame time and display-scale behavior have not been measured.

## 6. Resize and device pixel ratio

OfficeCanvas sizes its backing canvas using CSS size and devicePixelRatio and observes layout resizing. The source review did not find a complete DPR-change listener path for a display-scale change that leaves CSS size unchanged. PixelEditor and other canvas utilities also apply DPR-aware sizing; their behavior should be considered separately rather than assuming all canvases share one lifecycle.

## 7. Audit boundary

No browser rendering, performance profiling, GPU inspection, DPR-change experiment or full asset decode test was performed. Findings and remaining cross-references are in DEEP-AUDIT.md.

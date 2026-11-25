# Rendering Engine

**Status:** STATIC RENDERING MAP — performance/lifecycle verification remains open.  
**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8

## 1. Rendering architecture

The office is a 2D pixel-art scene using Canvas 2D, not WebGL/Three.js. React owns the UI shell and overlays; the office renderer draws the world canvas using OfficeState and sprite/catalog data.

Principal path: OfficeCanvas -> OfficeState/editor render state -> office renderer -> tile/furniture/character/pet sprite data -> Canvas 2D.

The game loop drives animation/update work separately from React component rendering. Tool overlays and selected interaction UI may be DOM layers over the canvas.

## 2. Scene inputs

- OfficeState: agents, seats, tile map, furniture placement, pet entities and selection.
- Layout modules: floor/wall/furniture definitions, serialized layout and workstations.
- Asset stores: floor, wall, carpet, furniture, pet and external character sprite data.
- Agent activity: status, tool activity, permission waits, team/subagent state and provider capabilities.
- Editor state: active tool, selection, hover/cursor, placement previews and editor render state.

## 3. Character rendering

Character studio is a separate subsystem:
- wardrobe.json defines part categories and palettes.
- loadWardrobe.ts eagerly imports/inlines PNG art and decodes the wardrobe.
- characters/renderer.ts composes profile layers into sprite sheets and maintains caches.
- SpriteCanvas/Stage/PixelEditor draw sheets and edit frame pixels.
- Pixel edits are uncoloured edits associated with a piece/frame so they follow part color treatment.

The 81 built-in character PNGs have valid signatures and 128×96 IHDR dimensions. This is not full CRC/pixel validation.

## 4. Asset loading

Production: server assetLoader -> core PNG decoders -> LoadedAssets messages -> useExtensionMessages -> furniture catalog and floor/wall/carpet/pet/character stores.

Vite development: plugin exposes decoded asset JSON endpoints; browserMock injects equivalent messages through WebSocketTransport.deliver(). The development mock is not production asset loading.

The 25 public furniture manifests and sprite assets were reviewed. Public asset dimensions are in ASSET-INVENTORY.md.

## 5. Cache and lifecycle findings

- WV-O04: furniture catalog, sprite/colorization/carpet caches use module-global mutable state with inconsistent bounds/reset behavior.
- WV-ASSET-03: empty dynamic catalog rebuild can preserve old module-global catalog state.
- WV-ASSET-04: colorized carpet cache has no visible capacity/eviction bound.
- WV-C10: character renderer caches have differing bounds; beard cache growth remains to assess.
- WV-WARD-01: rejected wardrobe load Promise remains memoized and blocks retry.
- WV-C02: LoadingScene cache lifecycle needs bounds/disposal analysis.
- WV-O05: ToolOverlay may trigger React state updates each animation frame.
- WV-C01: IntroBubble may trigger React state updates each animation frame.
- WV-O06: OfficeCanvas DPR changes may not be detected when CSS dimensions remain constant.

These are static observations; memory growth, frame time and display-scale behavior have not been measured.

## 6. Resize and device pixel ratio

OfficeCanvas sizes its backing canvas using CSS size and devicePixelRatio and observes layout resizing. The source review did not find a complete DPR-change listener for a display-scale change that leaves CSS size unchanged. PixelEditor and other canvas utilities apply DPR-aware sizing too; their lifecycle should be considered separately.

## 7. Audit boundary

No browser rendering, performance profiling, DPR-change experiment or full asset decode test was performed. Findings and remaining cross-references are in DEEP-AUDIT.md.

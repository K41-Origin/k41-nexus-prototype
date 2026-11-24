# Webview UI — Public Asset Metadata Inventory

**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8  
**Scope:** 60 PNG files under webview-ui/public; all fetched as base64 and checked for PNG signature and IHDR width/height.

## PNG inventory

### Public identity/app images

| File (relative to public/) | IHDR dimensions | Result |
|---|---:|---|
| apple-touch-icon-precomposed.png | 256×256 | PNG signature valid |
| apple-touch-icon.png | 256×256 | PNG signature valid |
| banner.png | 1456×480 | PNG signature valid |
| characters.png | 1200×144 | PNG signature valid |
| office.png | 1039×653 | PNG signature valid |
| icons/icon-256.png | 256×256 | PNG signature valid |
| icons/icon-512.png | 512×512 | PNG signature valid |

### Carpets

| File (relative to public/) | IHDR dimensions | Result |
|---|---:|---|
| assets/carpets/carpet_0.png | 64×64 | PNG signature valid |
| assets/carpets/carpet_1.png | 64×64 | PNG signature valid |
| assets/carpets/carpet_2.png | 64×64 | PNG signature valid |

### Floors

| File (relative to public/) | IHDR dimensions | Result |
|---|---:|---|
| assets/floors/floor_0.png | 16×16 | PNG signature valid |
| assets/floors/floor_1.png | 16×16 | PNG signature valid |
| assets/floors/floor_2.png | 16×16 | PNG signature valid |
| assets/floors/floor_3.png | 16×16 | PNG signature valid |
| assets/floors/floor_4.png | 16×16 | PNG signature valid |
| assets/floors/floor_5.png | 16×16 | PNG signature valid |
| assets/floors/floor_6.png | 16×16 | PNG signature valid |
| assets/floors/floor_7.png | 16×16 | PNG signature valid |
| assets/floors/floor_8.png | 16×16 | PNG signature valid |

### Furniture

| File (relative to public/) | IHDR dimensions | Result |
|---|---:|---|
| assets/furniture/BIN/BIN.png | 16×16 | PNG signature valid |
| assets/furniture/BOOKSHELF/BOOKSHELF.png | 32×16 | PNG signature valid |
| assets/furniture/CACTUS/CACTUS.png | 16×32 | PNG signature valid |
| assets/furniture/CLOCK/CLOCK.png | 16×32 | PNG signature valid |
| assets/furniture/COFFEE/COFFEE.png | 16×16 | PNG signature valid |
| assets/furniture/COFFEE_TABLE/COFFEE_TABLE.png | 32×32 | PNG signature valid |
| assets/furniture/CUSHIONED_BENCH/CUSHIONED_BENCH.png | 16×16 | PNG signature valid |
| assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_BACK.png | 16×16 | PNG signature valid |
| assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_FRONT.png | 16×16 | PNG signature valid |
| assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_SIDE.png | 16×16 | PNG signature valid |
| assets/furniture/DESK/DESK_FRONT.png | 48×32 | PNG signature valid |
| assets/furniture/DESK/DESK_SIDE.png | 16×64 | PNG signature valid |
| assets/furniture/DOUBLE_BOOKSHELF/DOUBLE_BOOKSHELF.png | 32×32 | PNG signature valid |
| assets/furniture/HANGING_PLANT/HANGING_PLANT.png | 16×32 | PNG signature valid |
| assets/furniture/LARGE_PAINTING/LARGE_PAINTING.png | 32×32 | PNG signature valid |
| assets/furniture/LARGE_PLANT/LARGE_PLANT.png | 32×48 | PNG signature valid |
| assets/furniture/PC/PC_BACK.png | 16×32 | PNG signature valid |
| assets/furniture/PC/PC_FRONT_OFF.png | 16×32 | PNG signature valid |
| assets/furniture/PC/PC_FRONT_ON_1.png | 16×32 | PNG signature valid |
| assets/furniture/PC/PC_FRONT_ON_2.png | 16×32 | PNG signature valid |
| assets/furniture/PC/PC_FRONT_ON_3.png | 16×32 | PNG signature valid |
| assets/furniture/PC/PC_SIDE.png | 16×32 | PNG signature valid |
| assets/furniture/PLANT/PLANT.png | 16×32 | PNG signature valid |
| assets/furniture/PLANT_2/PLANT_2.png | 16×32 | PNG signature valid |
| assets/furniture/POT/POT.png | 16×16 | PNG signature valid |
| assets/furniture/SMALL_PAINTING/SMALL_PAINTING.png | 16×32 | PNG signature valid |
| assets/furniture/SMALL_PAINTING_2/SMALL_PAINTING_2.png | 16×32 | PNG signature valid |
| assets/furniture/SMALL_TABLE/SMALL_TABLE_FRONT.png | 32×32 | PNG signature valid |
| assets/furniture/SMALL_TABLE/SMALL_TABLE_SIDE.png | 16×48 | PNG signature valid |
| assets/furniture/SOFA/SOFA_BACK.png | 32×16 | PNG signature valid |
| assets/furniture/SOFA/SOFA_FRONT.png | 32×16 | PNG signature valid |
| assets/furniture/SOFA/SOFA_SIDE.png | 16×32 | PNG signature valid |
| assets/furniture/TABLE_FRONT/TABLE_FRONT.png | 48×64 | PNG signature valid |
| assets/furniture/WHITEBOARD/WHITEBOARD.png | 32×32 | PNG signature valid |
| assets/furniture/WOODEN_BENCH/WOODEN_BENCH.png | 16×16 | PNG signature valid |
| assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_BACK.png | 16×32 | PNG signature valid |
| assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_FRONT.png | 16×32 | PNG signature valid |
| assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_SIDE.png | 16×32 | PNG signature valid |

### Pets and wall

| File (relative to public/) | IHDR dimensions | Result |
|---|---:|---|
| assets/pets/claudio/pet.png | 96×96 | PNG signature valid |
| assets/pets/gitcat/pet.png | 96×96 | PNG signature valid |
| assets/walls/wall_0.png | 64×128 | PNG signature valid |

## Other binary and metadata files

| File | Evidence | Status |
|---|---|---|
| Screenshot.jpg | JPEG SOF dimensions 1308×521; tree size 111,189 bytes | Header/dimensions verified |
| favicon.ico | ICO directory has 3 entries: 16×16, 32×32, 48×48; 32-bit entries | Header/directory verified |
| fonts/FSPixelSansUnicode-Regular.ttf | Git tree size 1,077,160 bytes; base64 fetch returned empty content | NOT VERIFIED — sfnt tables, glyph coverage and metadata not inspected |
| assets/default-layout-1.json | Inventoried; schema/content validation belongs to layout audit | Content audit pending |
| manifest.webmanifest | Inventoried; referenced icon/manifest semantics require final cross-check | Content audit pending |

## Asset manifests

All 25 furniture manifest.json files were previously fetched/read. Their referenced sprite dimensions were checked against corresponding PNG dimensions in prior batches; no mismatch was reported. Pet manifests for claudio and gitcat were also read. Office bubble JSON manifests under src/office/sprites were read separately.

Furniture manifests: BIN, BOOKSHELF, CACTUS, CLOCK, COFFEE, COFFEE_TABLE, CUSHIONED_BENCH, CUSHIONED_CHAIR, DESK, DOUBLE_BOOKSHELF, HANGING_PLANT, LARGE_PAINTING, LARGE_PLANT, PC, PLANT, PLANT_2, POT, SMALL_PAINTING, SMALL_PAINTING_2, SMALL_TABLE, SOFA, TABLE_FRONT, WHITEBOARD, WOODEN_BENCH, WOODEN_CHAIR.

## Validation boundary

- PNG checks validate signature and IHDR dimensions only; full PNG decode, CRC, color profile, transparency and pixel-level content checks were not performed here.
- TTF internals remain unverified; Git tree size/blob SHA alone is insufficient.
- Public asset consumer mapping and runtime loading are documented separately; dynamic loading was not tested.

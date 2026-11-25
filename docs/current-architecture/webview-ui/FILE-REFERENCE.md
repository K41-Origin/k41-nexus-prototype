# Webview UI — File Audit Ledger

**Baseline:** 4dd14d612fd37d89ffbbc977959f572395c4eec8  
**Last SHA reconciliation:** 2026-11-25 at source `main` commit `56f354f2808ae5aba10b0e1d5892bd9fa7ca095b` (tree `2d02bb29bd5a3aa8f74fc6a2ed21027149817e50`); recursive tree `truncated: false`.  
**Inventory:** 459 tracked blobs (302 src, 92 public, 54 tests, 11 root/package/config).  
**Reconciliation result:** 459/459 ledger paths and blob SHAs match the current tree; 0 missing, 0 extra, 0 mismatched, and 0 source changes against the baseline.

Compare current blob SHA before any re-read. Unchanged files with reviewed status must not be re-read for the same purpose; continue only documented gaps/cross-references. Changed files require targeted re-audit.

Statuses are conservative. REVIEWED REPORTED preserves previous session work without overstating exact line coverage. INVENTORIED does not mean audited. The reconciliation above verifies file identity and coverage in the ledger; it does not promote any review status.

| Path | Baseline blob SHA | Bytes | Status |
|---|---|---:|---|
| webview-ui/.gitignore | a547bf36d8d11a4f89c59c144f24795749086dd1 | 253 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/eslint.config.js | 93781c994f6a2e888e86d5574224a4ef4574fcb0 | 1931 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/index.html | 723178f03fb4d89f2d75c0a0b78dd0704f2f9633 | 1822 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/loading.html | ba4178b911341971cb2d317bcb3ae0e93911bd46 | 487 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/package.json | 3729872b2736eaf6b66c608c1b40f4e24c25d2fb | 1113 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/public/apple-touch-icon-precomposed.png | 9d7154cbc4ef79c2cf08efa054120c9886c38fdf | 3420 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/apple-touch-icon.png | 9d7154cbc4ef79c2cf08efa054120c9886c38fdf | 3420 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/carpets/carpet_0.png | 67fe2cd77038247a84c1a66fb9592ba3ee41a780 | 394 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/carpets/carpet_1.png | 25c9c0105fc0373cfcf89176101035bf64a5010c | 380 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/carpets/carpet_2.png | 28907dc0181b713bf9da57cfac6148a7f7ca6122 | 363 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/default-layout-1.json | 2954e54dede76f70dcf284a703b4c7c2b6ae4cc7 | 14425 | REVIEWED — JSON parsed; grid/tile/color counts and furniture coordinates validated |
| webview-ui/public/assets/floors/floor_0.png | 8d7a03f6ec7d803f1bbc9ad66c99413952091b01 | 1719 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_1.png | 157e09447a325aafe9acc92a6ef4c3a1cd4638e8 | 3590 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_2.png | 0802a3aefc90702acce77281ea02adb1ff09cd98 | 3590 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_3.png | af976465e32a3ab3cc08f7e1eea79eafd4dcd612 | 3594 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_4.png | 470fc91a37e1a6ab22a1887258a6558ef84c2fe0 | 3594 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_5.png | 7517dcbd20172867a65bdabf01716eaaf83a65ca | 3597 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_6.png | fef67c4f8e7621616fd2bfab9e3c0d4e808b61d2 | 3597 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_7.png | d06893948c00690c94bacbdae177726ff6183904 | 3596 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/floors/floor_8.png | 49ec35c31bed8448a70ecb1a2243255297e97bb0 | 3592 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/BIN/BIN.png | a378be46f9e062cd6ceae5276495c32d874fc081 | 252 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/BIN/manifest.json | 768d2ffd8c1f4890917615792905490b66115076 | 227 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/BOOKSHELF/BOOKSHELF.png | 165f4181486219978edbde1d505fc938161cda58 | 388 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/BOOKSHELF/manifest.json | c92161fd66e3a35942323b5ced910729e89f4e84 | 238 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/CACTUS/CACTUS.png | bbe4568b8bec175eec4327831cbc4ac1beabf790 | 558 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CACTUS/manifest.json | 8e53f90631c34f7ceff584f97a08df5e8114bec8 | 234 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/CLOCK/CLOCK.png | 549db9405fdb3155f0c43fb9c8eeb2d5cce1396d | 304 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CLOCK/manifest.json | b49a000eb2cf4c82e734bffc2c8680ebcc11dc58 | 230 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/COFFEE_TABLE/COFFEE_TABLE.png | b1bae849ecaeda3e993903e379b47ecba26634e9 | 274 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/COFFEE_TABLE/manifest.json | 9aecce7f790e228d5121831a652b83f0f3386050 | 246 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/COFFEE/COFFEE.png | ebc245418c489a25174ca7b78b3db216c4fef1bb | 223 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/COFFEE/manifest.json | 817abe99ea8e7a561ef6379f183ebffbee24aa90 | 233 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/CUSHIONED_BENCH/CUSHIONED_BENCH.png | 9785551e4c59555ca04efaab39e2b6e40c7c356a | 250 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CUSHIONED_BENCH/manifest.json | 6ab3b9932555e9382ee8f4e3b6d55091eed7c27e | 253 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_BACK.png | e2bce3b7a58c8602f509ee0da9e51bc720451f43 | 205 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_FRONT.png | 3e6848a4ac6c151a20d4cdfd7202e394f12eece5 | 247 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CUSHIONED_CHAIR/CUSHIONED_CHAIR_SIDE.png | 242cb432d3de69fce5733442dbb5460389a1503c | 255 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/CUSHIONED_CHAIR/manifest.json | 7d2c32f65d12f713a65c252254d8f083cf44db20 | 976 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/DESK/DESK_FRONT.png | 1d6b4406ce40ff4c29257fcd97c631cce899f516 | 310 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/DESK/DESK_SIDE.png | c9adb6691d0e16aa1d1a724791975779c9e25a2b | 278 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/DESK/manifest.json | 0f2a098c57c8a150193d84359c84c48f1b1205f8 | 649 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/DOUBLE_BOOKSHELF/DOUBLE_BOOKSHELF.png | f406a7b29483acd4ff72ccd6d7fed9907aea094d | 627 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/DOUBLE_BOOKSHELF/manifest.json | 78eaac126fc0c8021015280addd4378ca07eb1c7 | 252 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/HANGING_PLANT/HANGING_PLANT.png | 538e1e909aa3f64ba9064686e887187cbb8c7d05 | 693 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/HANGING_PLANT/manifest.json | 03cd6b75464eb06aafba1a2fd1695961033affae | 246 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/LARGE_PAINTING/LARGE_PAINTING.png | f97e17b4be3e4667a9d2a761494e65fce7bad3dd | 1056 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/LARGE_PAINTING/manifest.json | b47db847e8010ef8962030890c2d6b94b91756c0 | 248 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/LARGE_PLANT/LARGE_PLANT.png | f70812372e1f8b2d35b6110a409ff1443a40769e | 1285 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/LARGE_PLANT/manifest.json | fb2bf5f36561f9d719a4f41259bb5ea73e30fe51 | 245 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/PC/manifest.json | 4e7a90cb92a0788771bd6fb615414ef247ce37fa | 2025 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/PC/PC_BACK.png | 773c1fd5fa4889836e2dc0db7b7cd05ad790f945 | 349 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PC/PC_FRONT_OFF.png | ad93288181d2f0e2fc3b6671b31b57d62b9b5c9a | 427 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PC/PC_FRONT_ON_1.png | 7ed915bee741b5ca12f4109b84197f91842510cb | 479 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PC/PC_FRONT_ON_2.png | fe2e6ce4f377e85f24af221eb15efa4f1aaeea80 | 476 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PC/PC_FRONT_ON_3.png | db81e3c1eec7dd0c8d27cd915ad861f89238eaf3 | 485 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PC/PC_SIDE.png | 794466d90ce4cc2b2223dbdf1cb2b411c5bab553 | 451 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PLANT_2/manifest.json | 93b200b16c6564a0239b53196db77ae37815f755 | 234 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/PLANT_2/PLANT_2.png | 87c899bd8678e473404d3f1e5f39adc4478ff289 | 543 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/PLANT/manifest.json | 885866af8b09e97454c837f1e91c0705db84d3ce | 232 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/PLANT/PLANT.png | 5e6613e3ffc05896f0433436ef1faf2b8c007ffb | 703 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/POT/manifest.json | 83c624c55635d2b8b1051a89a47b211274099ce5 | 228 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/POT/POT.png | 183c390a816eae3b1014be2ba350253fb8dd71f0 | 288 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SMALL_PAINTING_2/manifest.json | 8ed7e3e8aedacc744943286475004b2ddaf0f0ab | 251 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/SMALL_PAINTING_2/SMALL_PAINTING_2.png | 7afdfb27027a6ce5ddb225cf505b000e1b0c596c | 473 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SMALL_PAINTING/manifest.json | c3b8cded89e75a2117b4cba19bd3f19ff6d75015 | 248 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/SMALL_PAINTING/SMALL_PAINTING.png | 66017ba7710aec9b2bdb09d8b1db0dd67a2427ca | 473 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SMALL_TABLE/manifest.json | eed5383ffe3cd8502973c9679da6011373cc4afd | 692 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/SMALL_TABLE/SMALL_TABLE_FRONT.png | dbbc97eb92fc799d2c192be4ff3016393319df9e | 240 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SMALL_TABLE/SMALL_TABLE_SIDE.png | 675490c5d7f70367ae6f228a297a7f44ab2bcc4d | 225 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SOFA/manifest.json | ecee707e40f227c06390f04fb4ca632433ed3dd9 | 888 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/SOFA/SOFA_BACK.png | 996fa46677852da3eff9f12d17c5ac4b87eae443 | 192 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SOFA/SOFA_FRONT.png | 9170c8a3954b4c6f2a1ba2fb35cd32dfec89ca43 | 210 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/SOFA/SOFA_SIDE.png | 77d1621665cef1121a90bb5a6d9df685436ca589 | 255 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/TABLE_FRONT/manifest.json | 1adefc09f02fa7ab0d6795c79276894eea8a0159 | 238 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/TABLE_FRONT/TABLE_FRONT.png | 2d32ea0fafbb5be6876ae16bc65a2ac3af1f45c4 | 400 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/WHITEBOARD/manifest.json | 49a1069f71cbb327ec868951b0c1bd70391f99dc | 240 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/WHITEBOARD/WHITEBOARD.png | c4b7484aa593f15327981741841dac92b09c7aca | 336 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/WOODEN_BENCH/manifest.json | 40230b3b5292c5a0b70baad8f349aecb3b597f5e | 247 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/WOODEN_BENCH/WOODEN_BENCH.png | e439027c1d9ffffe5f2f364b4ff10ac8bc72312d | 208 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/WOODEN_CHAIR/manifest.json | 4b6730a12210a51506e6caa2cfa2ed164ecce8a1 | 952 | REVIEWED — furniture manifests parsed |
| webview-ui/public/assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_BACK.png | 19921f909ce83565c5ab4164de6bd0f5b1e937af | 290 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_FRONT.png | 392f5a2758abdec2233da837f9c8ab1c271d7d82 | 295 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/furniture/WOODEN_CHAIR/WOODEN_CHAIR_SIDE.png | f9c58772117e776e99254fb87cc10d1f941d4d77 | 261 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/pets/claudio/manifest.json | 767da5eb3a31f03a4f27649c0a8cdaa8e19bd562 | 39 | REVIEWED — pet manifest JSON read |
| webview-ui/public/assets/pets/claudio/pet.png | 9433b28259850d8142e3d8e991b0548d20570f2e | 3267 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/pets/gitcat/manifest.json | a24e6e3d005a35b5dd7535da5ab61b3651a376f8 | 37 | REVIEWED — pet manifest JSON read |
| webview-ui/public/assets/pets/gitcat/pet.png | d2b7f9b5f39ca3d9106908ff206b7176f377627a | 2892 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/assets/walls/wall_0.png | 5530b2452c21671a4124eb2531855cf3e69c5b09 | 1738 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/banner.png | 8fc2fd94cdf1f9f6e2347c6fea26825e9f3ffd61 | 10752 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/characters.png | 371401d2a40cdfe89b6af38b991f130c0e9d4981 | 12216 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/favicon.ico | dd1c0a695a87dc36eee134181534eafc8d883b4a | 4926 | BINARY HEADER VERIFIED — ICO entries 16×16, 32×32, 48×48 |
| webview-ui/public/fonts/FSPixelSansUnicode-Regular.ttf | 767a03d1f7417bbfdde1b42ce580b7dcdbd2cc9e | 1077160 | NOT VERIFIED — base64 fetch returned empty; tree size/hash recorded |
| webview-ui/public/icons/icon-256.png | 9d7154cbc4ef79c2cf08efa054120c9886c38fdf | 3420 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/icons/icon-512.png | f596e5507098171db1803199b96707dc93a8c21e | 5057 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/manifest.webmanifest | 15b9bcef86a7819667804adafae815f9c0c2dcbb | 501 | REVIEWED — JSON parsed; two icon references match PNG IHDR dimensions |
| webview-ui/public/office.png | 8c770a3c15f4b67264d160b195fe24853234120e | 49958 | BINARY HEADER VERIFIED — PNG signature/IHDR; dimensions in ASSET-INVENTORY.md |
| webview-ui/public/Screenshot.jpg | a23439795da5ce4f0bffe11b8fff855700fb6b90 | 111189 | BINARY HEADER VERIFIED — JPEG 1308×521 |
| webview-ui/README.md | d2e77611fd3d959fee0487c41bd27b318be32b04 | 2555 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/src/App.tsx | 3eb91a2050da5f789223bc0a045a80fc841d60cb | 42910 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/browserMock.ts | 335caac9b428de7801b0c5c680eaab2dd7a1f290 | 8750 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-003; browser-mock asset fallback and dispatch flow traced |
| webview-ui/src/changelogData.ts | a7a9c67afa47a0fdab0c63de91a25e9ead3389be | 12006 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/characters/art/artColors.ts | 793a52107a0e9704cf5e4c657b52e56d3851bf04 | 967 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/art/beard/full_beard.png | ea50d209e15eb2176eb804604e72c25acefe7458 | 1247 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/beard/goatee.png | 4f1546fa94a981dfd7c8991c0663c5cb9701b0da | 653 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/beard/long_beard.png | d06f4942ce61ab385e2d4ecbf8d0c75af0e69421 | 1517 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/beard/moustache.png | c1d0d9deb69f2f21e39d5c47d1f4c97f67e746c7 | 373 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/beard/stubble.png | c0d8fc053e6df3d315efb1e453e12e382fed429a | 589 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/body/bust.png | 9e0a6da18139d3cebdc9d678f96f35e89a75e620 | 9595 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/body/flat.png | 477422f7beb4f7b1e1940202bc50342ff22759ce | 9586 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/earrings/earrings.png | 42d023173ac8d3ccb65c97510fe03e11a7964b33 | 465 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/closed-stern/lashes.png | 963251a28ba8d16ff1155970172c3c82206cdec7 | 352 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/closed/iris.both.png | 11b8e3b4f6fea3fbf01bafc7eb82b694596690da | 145 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/closed/iris.top.png | 11b8e3b4f6fea3fbf01bafc7eb82b694596690da | 145 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/closed/lashes.png | a2aa79e704d28e124582e94c6dffb99fdcf68089 | 203 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/closed/whites.png | a14328face66041825dd2b1a7a238ac06721e75a | 145 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lashes/iris.both.png | 69a71f857eda7a5d3c0e7ba855fbebf2f2b30720 | 240 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lashes/iris.bottom.png | de4fdc0c0757d88e7bf66b351ffba2bc10625c58 | 205 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lashes/iris.top.png | 6ff366150d79b609d7ea0bda1997cc7ee77746e4 | 205 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lashes/lashes.png | 0af3efc126cd872ecdc2f93a84c0a2fc715d91d5 | 233 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lashes/whites.png | 24ed4a67cfe995fab93f1e89dc8d2b18bd9a6acd | 252 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/lidded/lashes.png | 0c52515ab1041fb1771c68ef4e1ecfda0a47e683 | 272 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/plain/iris.both.png | 69a71f857eda7a5d3c0e7ba855fbebf2f2b30720 | 240 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/plain/iris.bottom.png | de4fdc0c0757d88e7bf66b351ffba2bc10625c58 | 205 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/plain/iris.top.png | 6ff366150d79b609d7ea0bda1997cc7ee77746e4 | 205 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/plain/lashes.png | f8853563e3cca89a4a3f1a712f5c8fce88bdf76d | 185 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/plain/whites.png | 24ed4a67cfe995fab93f1e89dc8d2b18bd9a6acd | 252 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/eyes/stern/lashes.png | b0c44bb7b8a66ee226f7658c556e5acc61f147cd | 470 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/full/business_suit.png | 445c9f1002bafb63bc783aa31bc49c85f76a1a27 | 2847 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/full/dress.png | e049ac1184d7f3b07330c067facfa91775b0ee98 | 2732 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/full/fitted_dress.png | 654f59c84eece8508b0efcb479b14e8c82798f84 | 2045 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/full/suit_and_tie.png | 27ded47d0116f89d7f9fc766f61f5aa391f8c472 | 1237 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/bob.png | ff0fe49c8c967d104227d150a36260ae49cdc489 | 7415 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/bowl_cut.png | 6df557d48b698e55bfb8711ab8ceb68e56b33134 | 2577 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/high_ponytail.png | 8b5623e5a81940268742f36ec067f71e725d99f8 | 6049 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/long_bangs.png | 2512bb860a43d4badaf673fbea0d438138813d3d | 7234 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/long_waves.png | 69cc8ab2156606f621f39dda1b51250370ff7fea | 9114 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/long.png | 834c62e5dc5da20dc2289b2b0c4e8c4b391da570 | 7281 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/low_ponytail.png | 070094ff6832ac531887a73133e197eeeba924a2 | 6187 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/mop.png | dfdce4574ec61a747204e88fd588a47a6fdebb37 | 5338 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/parted.png | 511c0fa3f250cd59eba3c3a1f457ede173bb886f | 4663 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/short.png | c9949e52dfaf714606d57822f1d07c12efa1c00d | 5104 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/shoulder_length.png | 0f36d5f4bbddae2b08cb06f2f4082194ed4c1adc | 5168 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/side_swept.png | f94c1c799bbf7293018799e97e2ed94527acbf79 | 3339 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/slicked_back.png | cc7d00c53698eb5530e4d1fa083425af3123e4e6 | 5065 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hair/spiky.png | 89abdc0a4c65e8c64e6564c7ae624c5772d24140 | 5619 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hairshades/hairshades.png | cfd0a8a579360b57bbb715bab0c74ec810d16296 | 1001 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hat/badge_cap.png | b32e0520178d77edd5e866d5b1ca85dd268df496 | 4140 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hat/flap_cap.png | 4453000755951f9b81360f6cc4eb054ec7cea750 | 3152 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/hat/plain_cap.png | 51ae7621354ed171d56a952a55c39070fdfbea48 | 3196 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/jacket/blazer.png | 879b604b5e092d5ae9e5504316190c465ca4fc5a | 1778 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/jacket/bomber.png | 603c39c701baf36aa79f5bbb8054c82a0cb38c1d | 2119 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/jacket/long_coat.png | 06dcf0d4987549985159bac9666d2bbbf1dc888f | 1134 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/jacket/open_shirt.png | fec6504960b3a983a5ceb4b925215bd63435010f | 2092 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/jacket/vest.png | bc369f2bd44cd7721f410cd3bc36b482c318868f | 1011 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/lanyard/lanyard.png | 3be12cf2b46e527598bb851b31f2e1b367a1394a | 400 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/necklace/necklace_chain.png | 7daa58e6a1ea81c983a5631940b6fcde37501d33 | 438 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/necklace/necklace.png | 593c8714c53fa57f73a8a2166d716f9cab56ac1d | 629 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/fitted_shorts.png | 995e1bff13c88e923099f48ae43c0db683048ddf | 1118 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/jeans.png | 96617d27ab443a0932743de18cfcd6eee7addccb | 1508 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/long_skirt.png | 5dee8d0570a40993fcc988a0bbb8356aba006308 | 1021 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/low_cut_jeans.png | 96e8a0097d0399b79a8c8d66df523a451f401c81 | 1342 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/short_skirt.png | f5ed73da7fddb28f5ba4f8e929266635d4dc66ee | 890 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/shorts.png | e7c86cefc1f3ca9c2e8ea512c568eee601b74da7 | 967 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/pants/skirt.png | d0717bf9a62e00b7e500b35c6851a3cc567dc510 | 1165 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/props/paper.png | 5c22241412906cacc9c2210215bccd28dba8e419 | 417 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/props/reading-glasses.png | dbe94941d37eb9758713f83ff9b562d637a54fad | 216 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/shades/glasses.png | ce387bf2c47a9689122fdf099408ba1199e2cb6b | 676 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/shades/shades.png | 4611020ad20b73c8d898bfeac8c321317bdab817 | 932 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/shoes/boots.png | 3b76a95fbf3812ad8d904489c365290b4277de0a | 978 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/shoes/sneakers.png | e8645e5c5f473bb7bf5b2d5ace9061e44eb509b7 | 778 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/tie/suit_and_tie.png | 63596683049e05f7b2552ffea19d68a603313e99 | 250 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/top/long_sleeved_shirt.png | 246f9ceb7a40894c3106d05d6da2d28d76b625c7 | 1885 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/top/tank_long.png | 0e8dab050b47be75aef24a6de303113ec43408be | 1223 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/top/tank_top.png | dcae82490a361148185541b890e53464f6288c8a | 1032 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/top/tshirt.png | af256205c19032d1b8f30513f312b83617b660a8 | 1874 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/top/turtleneck.png | c49287244ce8b2f4eca149322dc5cc9cc09d46a8 | 2199 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/under/bust.png | 992f9175334aa51b38f715842253615992cb6632 | 849 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/under/flat.png | 992f9175334aa51b38f715842253615992cb6632 | 849 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/utop/bust.png | 967417949fec6624a2e4d773eaf0e98eb3b00f18 | 1225 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/utop/flat.png | 967417949fec6624a2e4d773eaf0e98eb3b00f18 | 1225 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/wardrobe.json | 9c99b8de8a6f1a3e070226f6a8c230ea863b21e7 | 159648 | REVIEWED — JSON parsed; manifest consistency checks passed |
| webview-ui/src/characters/art/watch/watch_both.png | 9d2b270f75fae0a4e69ac6fca8acdc739c2261ee | 416 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/watch/watch_right.png | aee2e0eef4862a8a54cc3fd6884e10af8c2eddc0 | 314 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/art/watch/watch.png | f4742d7f188ef0ed6ed77e35dec722779be5a59d | 339 | BINARY HEADER VERIFIED — PNG signature and 128×96 IHDR; full CRC/pixel validation not performed |
| webview-ui/src/characters/cast.ts | b923a8bd52a572f0e9de11ecd6474642a3561346 | 13564 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/castMessages.ts | 35bdf9a46246ffa2ef60cdb2fe377b3761ff5312 | 2031 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/castStore.ts | 5f3186567cd783f727b487543b4e9a1157e0749c | 3181 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/color.ts | 1746fa07996a6e75df46381f77f01f6f7c19e9a7 | 4371 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/history.ts | 43b3e623c84517f71c70efe40775ece579be1ceb | 2695 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/items.ts | 61bcc273528234f93c8f3f596fa271dd3e830458 | 4790 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/loadWardrobe.ts | 213317f090a1f02bce586e7becbd21f7fbf8a48e | 1766 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/officeSprites.ts | 4b175d47d4ef2393b59c89d7ad71b1ac21a81c48 | 5598 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/profiles.ts | f7db8a40143d302e9f6c5c7f8c828b6b89716e43 | 9099 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/recent.ts | 5697a89ae83884bfb5796f9558a450520d8b08dd | 723 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/renderer.ts | 41219fc5bad2f2bd70b7c49ead8f6f571e18c8d5 | 16950 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/repeatClothes.ts | c3d6825b008b1e1a4e25a1b6980be7727c4538ff | 2131 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/sheet.ts | 17296fd4d477ce97d5a6cbbd8445231539ab65a5 | 1675 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/slots.ts | 51f4fb5a83f7391d5190db6c79916139221e0ecc | 14264 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/ArmedButton.tsx | b773162e20ae6aa3620bfb4ae0da08e8fb00981c | 1195 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 40 lines verified |
| webview-ui/src/characters/ui/castActions.ts | b18caf1f4759c28a9b0be83d9584aaa3190dd839 | 4181 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/characters.css | b299ebb659f58b4b253214c18761c8d1d18c081e | 19917 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 928 lines verified |
| webview-ui/src/characters/ui/characters.phone.css | 269248f4433198dee264e72ddc5896cd4611ff59 | 8159 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 373 lines verified |
| webview-ui/src/characters/ui/CharactersView.tsx | 5229b74d09d3a92f33ab8da530dfc995a7c0a5a5 | 15896 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/ColorPanel.tsx | c944c2c7aa40d00021f4689763e15472bb8fd6e6 | 13348 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 376 lines verified |
| webview-ui/src/characters/ui/files.ts | e9842275fee39cc3709453329b74a5f22d809281 | 4472 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/ItemTools.tsx | 68cc48ad0097c7b4ee3096fa02f7447c0d465cb8 | 5447 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 151 lines verified |
| webview-ui/src/characters/ui/pixelCanvas.ts | c570bffd712422fc8130c9ae0c33aeb43ea82c80 | 5867 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/PixelEditor.tsx | e422e2c2958d5c23c222b83c0bd5c03f53754a49 | 16518 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/Roster.tsx | f92a91c7fce45bd5f9af08a7ddcfd040c051db76 | 9293 | REVIEWED — prior body inspection retained; EOF/tail boundary verified in this continuation |
| webview-ui/src/characters/ui/SaveButton.tsx | d96773f83779aecc12d3ba64ea43f6fbcdf84ea8 | 675 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 24 lines verified |
| webview-ui/src/characters/ui/SpriteCanvas.tsx | e4c199fa8873f867aace1ef2efd85e9e53f5c555 | 1091 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/Stage.tsx | 951954909bacc2ed6f9e722be90ba7dd45a52918 | 10571 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/characters/ui/studio.ts | 815583783ab3c2150230bce1c20934177832f36b | 2641 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 79 lines verified |
| webview-ui/src/characters/ui/ViewButton.tsx | 9c22f04ee2deb6489666128247122b5588201b7b | 996 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 34 lines verified |
| webview-ui/src/characters/ui/Wardrobe.tsx | 5fb4e456225f297b23400e26363ae84fe09714a0 | 8912 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-007; 275 lines verified |
| webview-ui/src/characters/wardrobe.ts | 6d2d2bdd35888b71b515971ff93f78156f45810c | 8926 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/AgentCard.tsx | fe7be6e720b6cd8a7f7a3b1e75e209c651fd6723 | 4312 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/AgentCardBar.tsx | 82449d892429b10bd26f1fa7dd9485a281cb9022 | 2573 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/AgentCardList.tsx | e55b618eb2a37382a6c6daf60bb9f67367bf31f3 | 4833 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/BottomToolbar.tsx | 95472e7199aa8236ff2603c1460df1c65b4f8307 | 10051 | REVIEWED — prior body inspection retained; EOF/tail boundary verified in this continuation |
| webview-ui/src/components/cardBar.ts | 76ac0818de593966b7c73ef60e0c11fde42470a4 | 3043 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/ChangelogModal.tsx | e550bd6d1a15ab95d1777b171c18a8b0aa376a70 | 4744 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/CharacterMugShot.tsx | ed982e8c4d46377685a5c4cdd0a2db0d6836712b | 2401 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/CharacterPicker.tsx | 96d325100bb3e300829696fbefe4d97f65fd117c | 12288 | REVIEWED — prior body inspection retained; EOF/tail boundary verified in this continuation |
| webview-ui/src/components/ConnectionIndicator.tsx | 805b2b1dde3c69d81f06672a5656e3d86c31307e | 1391 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/DebugView.tsx | ccc1f465d3777b65bfb79c870d5cb6eb3ef95c5e | 6636 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/DirectoryCharactersField.tsx | 8dbc69a8f3fa51090a922af32848cc9d6a65d442 | 6310 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/DirectoryCharacterStrip.tsx | ea7b710df81527fb9ea8306e4208fded8bf0c3e7 | 5768 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/DirectoryModal.tsx | 29318c52dd6bdc50441fb9282bf6a1f3921f9635 | 9181 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/directoryStrip.ts | fe4746c500da2a7ae091324e2f8232dd429655c9 | 1701 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–46; cross-module/runtime verification remains open |
| webview-ui/src/components/EditActionBar.tsx | 167f33ecabb947684fd37f4c083ecf75c27e701d | 2187 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/IntroBubble.tsx | 3ecdac98e9e15be0eca5fe1f1ec848828ddee53a | 18970 | REVIEWED — prior body inspection retained; EOF/tail boundary verified in this continuation |
| webview-ui/src/components/introBubbleGeometry.ts | e8750b34e3459ee3d1ecc0493a473424b2898012 | 4883 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–109; cross-module/runtime verification remains open |
| webview-ui/src/components/LaunchDrawer.tsx | a4367269738709c51c0f82bdd82ea801dcfb0091 | 11148 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/LoadingScene.tsx | 764d5b9eb0a3dea6c32979b7e95e6cc439f6ff9b | 7622 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/loadingSceneArt.ts | 2d33cd9f1fad94103f20e22622c3dd58a88bba16 | 3451 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–97; cross-module/runtime verification remains open |
| webview-ui/src/components/loadingSceneLayout.ts | a122bbff64ef37b31bbc3daf046a977b01ba0f9f | 7905 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–223; cross-module/runtime verification remains open |
| webview-ui/src/components/MigrationNotice.tsx | 0aa1bd3190d5a50d7a20965d9b3250399a90e319 | 1237 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–35; cross-module/runtime verification remains open |
| webview-ui/src/components/MobileAgentBar.tsx | 6fc6be39ffb350cfd8090e0252c89f9cb7c7a9e2 | 8624 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–196; cross-module/runtime verification remains open |
| webview-ui/src/components/MobileKeyBar.tsx | d3a0d3f03d998577feacc056b6edf458ba456ddc | 8466 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–196; cross-module/runtime verification remains open |
| webview-ui/src/components/MobileTerminalPage.tsx | 42f686b412cafc90b4164cf38431cc2a0f2fd26e | 2163 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/SettingsModal.tsx | ca9248f01ba8631a81b0489fcb152203f3b553d3 | 11109 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/statusDot.ts | 23c78e9f495dbcdfc9031f37f95776f8d337d9dc | 556 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–11; cross-module/runtime verification remains open |
| webview-ui/src/components/TerminalDrawer.tsx | a2cf945de98cf1ba9838d7fb7c7011ac5d68fd84 | 3843 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/TerminalHideRibbon.tsx | 54f35f46fb05adb34c75c6fddeca595f3dceffa9 | 1945 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–52; cross-module/runtime verification remains open |
| webview-ui/src/components/TerminalPane.tsx | ae1cc4daca64ec437f283731e23629e6ecb66375 | 11493 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/TerminalPaneStack.tsx | 5862c638b345dfa430e91c084d7eb6110d543fcb | 3139 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/components/Tooltip.tsx | 5e2305434b2eb22075dea27fccec0324a942bde6 | 1502 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–39; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/Button.tsx | 0b04f4fef6ce11ef28cf18b19e05a491fe6432a1 | 1700 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–57; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/Checkbox.tsx | 8688f09731c4456cd7984a29131ebe3df7639722 | 1699 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–57; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/ColorPicker.tsx | b4e28664298b0e09e1ec36e3b3292c7e6adaf6e1 | 4737 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–159; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/Dropdown.tsx | 6bc0dbb17dbb81c7f390e8535a891827f4fd6d1a | 2890 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–82; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/ItemSelect.tsx | f7fa506384023e46c99f0ae333a536c9bde1c25e | 1334 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–54; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/MenuItem.tsx | db9a2cd11f5c48828b8cf75af1f284e70198a816 | 531 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–21; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/Modal.tsx | 527899e2657f222d4fd9c706d2414c35097ff364 | 3330 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–92; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/PickArea.tsx | 6ac096760f5d741376aaa5f82e0ca7d5baa9f8d4 | 1668 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–45; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/pickCanvas.ts | 7e8bbee9c3a7d4709f6494e9c1d85ae30e60d823 | 750 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–22; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/pixelFont.ts | 61e380a9b24a55f20aa4d6676cb58e73de73223d | 4622 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–128; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/pixelGrid.ts | 503040e24020218b4bd198019fcc1101c4c5c891 | 2132 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–72; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/PixelIcon.tsx | 62cc03bdc5f29d020c6592b011c6353d5abe051e | 1430 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–48; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/pixelIcons.ts | cc824e3123c1e78a4f0df461505904804a8d6c50 | 6109 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–339; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/PixelInputText.tsx | 94e7c2b2ed79e2831cdedd20ad3b835530860321 | 876 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–34; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/pixelSnap.ts | 93948494c9ea9f9feb519a49c89e5dfcfed007c0 | 1295 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–25; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/PixelSvg.tsx | 3eb7a942f1ea8d4a7a56d33e8629f3b0df1954a7 | 1071 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–42; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/PixelText.tsx | cd68c015f2b0711633e587a885a5a94c2613c607 | 2089 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–58; cross-module/runtime verification remains open |
| webview-ui/src/components/ui/types.ts | f83803bc75e7e3fa75a5d32af352bf4974d4ba7d | 493 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–14; cross-module/runtime verification remains open |
| webview-ui/src/components/useCardReorder.ts | 42092b1e069eaa60df8e88f097ba48fa4e4cef0c | 8578 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–224; cross-module/runtime verification remains open |
| webview-ui/src/components/VersionIndicator.tsx | ba5a0c3009bfb6f3028a4a5e786d5410eac5c4be | 4292 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–126; cross-module/runtime verification remains open |
| webview-ui/src/components/VisualColorPicker.tsx | f24cb4e20c3631ae740fa7c559d716b800213690 | 8840 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–254; cross-module/runtime verification remains open |
| webview-ui/src/components/ZoomControls.tsx | 7d3e17ed65d2e533b7e452b680c6f549eea947e5 | 5055 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-005; lines 1–156; cross-module/runtime verification remains open |
| webview-ui/src/constants.ts | 9580f95a5239485b5cb83a6ebe6c3b79010a19cc | 50697 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/conversations/ConversationList.tsx | 5f89f074a4e0bf4f60c926709b6cfd024fe7d94b | 16250 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/conversations/ConversationSearch.tsx | da5eb5efdd143572270909be52e680ae62f56b2c | 7084 | REVIEWED — conversationSearchResults correlation and searchConversations request path verified |
| webview-ui/src/conversations/ConversationSidebar.tsx | 08cff2d2fa11c8cb452ea3cdce438ba687606065 | 3391 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/conversations/recent.ts | 1aa092943dbcb83621368e66f6f5ebc4c5a63e59 | 834 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/conversations/timeAgo.ts | 8d02f7097b245b1e95face8d90756dee8281019a | 667 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/conversations/useConversations.ts | e360d9eb264502582c7a07f205fb0fc82910c8a3 | 2971 | REVIEWED — requestConversations, conversationsLoaded and refresh lifecycle verified |
| webview-ui/src/conversations/useConversationSidebar.ts | 1ce57afac2fb74b45f31238789c9f3313b355c6a | 1337 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/hooks/drawerWidth.ts | 4fa3b94f7e900af62e2eaafa26c4e6cb2257537f | 989 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/introTourState.ts | f64a1f9c2c598f812360413f958783fe1d90c2c5 | 5148 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/launchLatch.ts | d2eb0688e7eb637bba834c4ef4c6e3e1d4148649 | 672 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/launchMenus.ts | fad1fcd3196423896663ae3d0b8f3649ae56e8ec | 2417 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/slideTransitions.ts | 858df85c58b06c17d74c296ea31d9a5512e4d838 | 1417 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/useBoxSize.ts | abdfb290c54dfeda4cb98c5adc332eb95c2d55c2 | 841 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/useDevicePixelRatio.ts | 2aa6ee211e8f6a3082ae1d237dfd486ebc27ce04 | 912 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/useDirectoryEditor.ts | abf44c4dfc59fd57cdcc0193b31385a5e7bcebad | 7041 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/useDismiss.ts | 0910f0925e6afa283fed7ca25895ff19eea22e80 | 742 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useEditorActions.ts | 386b839a124ffad437c8a3cd9c3460e2826c0d00 | 28332 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useEditorKeyboard.ts | 44ccc408d1a264b45e99cb2c1056650910d3c8c7 | 3990 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useExtensionMessages.ts | 649025df90edef84d24c40556cee2ea007e15ccf | 44355 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/hooks/useFurnitureColorActions.ts | 8fbaa9bc141aaf2daeabc809da7132e6b1e36519 | 3796 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useHoverPin.ts | ed9f946d644ba8133beaec95e307b95136127304 | 1466 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useIntroTour.ts | 4fc18a2eef5bb2b73bcf6a4bf5facfc17d25a28d | 4367 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useIsMobile.ts | 6325a2b4dceaba556eaf113e1121dce279b8e93e | 1428 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useKeyboardViewport.ts | 7263411c15fa91fef5f4e944ea938b214ea3293e | 3674 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useLaunchActions.ts | 88d01bb2f03bade7f4f8030e25a34e90687bfbca | 1068 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useLongPress.ts | fb7c29dcdc42d2369d9237f654a2bb245aaa30d9 | 2262 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/usePageBackground.ts | 7bd272f4950a26b3908d4ae238ac9055459d44a6 | 922 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/usePresence.ts | 141a990f9c8ac9047b621b11e86ced25af7c3dfc | 1256 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useRosterLaunch.ts | 4988aca1cecc1b3f2f94de7fb2530449af4e567a | 5531 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useSlidePanel.ts | 2cf4ab0013cae1d82615759e51844e9f02bb7b98 | 2986 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-006; lifecycle/cross-module follow-up remains |
| webview-ui/src/hooks/useTerminalDrawer.ts | 2913c2331ad4bee5f127a249502a394bfb414980 | 10246 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-002; cross-module follow-up remains |
| webview-ui/src/index.css | daa1fcad26dbb03a9fa4ef0e1cf16389b7577b33 | 19712 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/loadingPreview.tsx | 9cd3169706acf23a2b3ea4a7d92fdc8b41fc855b | 364 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/main.tsx | 223bbf51073edc73b9646be0e9618b11ea1ac250 | 810 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-003; dev-only browser-mock bootstrap verified |
| webview-ui/src/mobile/edgeSwipe.ts | 37b4fbc95728f573cef28b8ae9c5792f32352652 | 7439 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/keyboardViewport.ts | bd8d9e880ffefbef922d335b2b72abde520c01db | 2884 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/MobileMenu.tsx | 24c49d3bebe365753451128e1c5dd35a845fe889 | 7450 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/MobileShell.tsx | 37b2f6d82b961a621a8a368f00a18d8446b75cd5 | 6862 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/useEdgeSwipe.ts | 3e79bdb28d92b7d4b158325f1a580cfa6a81d899 | 9283 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/useMobileMenu.ts | 785030b8168313d187efdebe413b8d4e9b699e6b | 520 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/mobile/useMobileShell.ts | df344a615db0da415fade0267ed68c3b39791008 | 3974 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/composer.ts | 4a40a45c0fa438a5afae526bb3d7128ecb705985 | 17481 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/moods.ts | 8c89b1734c0adc67194bcd090a0fa76da7aa6b04 | 7261 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/musicEngine.ts | 1017a20988fa1e92f215951e59d22d2b8cd7255f | 18153 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/MusicPlayer.tsx | 233414f5284f4f2491d545b1cda371b90a4694b7 | 11123 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/playerFit.ts | d96d12bcbaecccdadc45125ae8876ee1f5e426d0 | 1522 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/playerSheet.ts | 2b11abaf76cdc5335da51cf5769c02b5d0c664f2 | 2769 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/radioLift.ts | d0a4e3d5153d90f13f87a3c47e41c4d9b009d9be | 1324 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/RadioSettingsPanel.tsx | 4e6855d7ae81c958b6991ff5f0948e4a66de94e7 | 3114 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/rng.ts | 9514237693b38badf449fd5b87500c257d19f6a2 | 1090 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/songCode.ts | b0b42bddfbb41d89e4033d45619de7f94fda36fe | 1321 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/tuneNames.ts | 54e382e0747a5bcf5d407db54faca2a40da0e0ee | 5764 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/useOfficeRadio.ts | 81ce5a22c2220b0f4d429074844280628e94eb88 | 301 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/music/VolumeControl.tsx | ce5ee838cb311e662cab34693cfc75821b65de05 | 3705 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/notificationSound.ts | f30c00d2463e79724e7d57ad56420d92b2625211 | 3536 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/office/colorize.ts | 70496be0b26feb1805a140c5e3776588e810a66d | 7756 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/components/index.ts | af03421b7c9d3a3f1415f0a5571629f8e802c306 | 98 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/components/OfficeCanvas.tsx | 108b79553473434202deb68d06979d3d632a5815 | 30809 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/components/officeCanvasCursor.ts | 7192ca53bea50e4d0830da934a713986e6ae8706 | 1803 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/components/ToolOverlay.tsx | df2bc5c460e38597bb9f1503e1d992e0d5723091 | 15643 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/components/useCanvasTouchGestures.ts | 626b966e0777d6ffd4a2b162e3c485d631e4eece | 6181 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/ContextBar.tsx | 2c95d62668cd8b03206752b01f454855fbaea48f | 1398 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/contextGauge.ts | d7c029384a2ff6ce2eab002b0dad6f3d13525981 | 2865 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/editor/editorActions.ts | 18186c287beee8419577cbf087c164a7701c7cb5 | 29903 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/editor/editorRenderState.ts | 363b082b23b8582de6ad66ad65af01d6a0f92980 | 4528 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/editor/editorState.ts | 910d8142bf5d5d5724e9d69f17e2de5bb4f6ed4c | 10552 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/editor/EditorToolbar.tsx | a68edee76fabe00d7938f9428c4ad46e58bf9594 | 34072 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/editor/index.ts | 4494beb97b2cb1ec8a36b3eb35747b2f28293375 | 226 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/blink.ts | 01d27ae88f09d572b1944946ed43e823924ae1f6 | 1827 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/characters.ts | 5704a7bd8640ac4ab80de5534135b04b5687767c | 14457 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/existingAgents.ts | f23ff3f79f5fc6baf37f2915f8ff6abde107ce35 | 4346 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/gameLoop.ts | 998ad08b7ed2ed55e8788cd52aa179333d45601c | 1253 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/index.ts | 463adee87a48dac7eaad70bc7586290113c86fbc | 558 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/matrixEffect.ts | 5ce0add08ec716bf25f29af67c7bd2da39244dfd | 4897 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/matrixEffectState.ts | 28a94c827c73fe2554b7aa5176957d2b3cf19073 | 2136 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/officeState.ts | 1afd778f4c72f1901ef91266e29a89f117e180c7 | 50301 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/petEntity.ts | 7aa0e011a1f29b066ff847370344e9af18674232 | 10736 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/renderer.ts | ac7734937e5cf61cfef16b9710d4d1b0b7568bbb | 31769 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/seatPlacement.ts | 54c8752e5e19fcfa28fa0acf6c63cfe391088f15 | 1812 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/engine/selectionButtons.ts | 3544224cf54e5fb178004b13065b896aa0e07f1d | 3763 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/floorTiles.ts | 8375997529e5bd8c7c8ef1d24f0a6e73fe8d753e | 3061 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/exportLayout.ts | 2c9ec6353e93ed8510c91cda9cfbfdf21e6a29f2 | 1031 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/furnitureCatalog.ts | 727b7aa605cc201fbf562d9c985fda4186f0f2d2 | 15244 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/index.ts | f7c6ca993429c5a0f214f00bb7b5215f65796af5 | 467 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/layoutSerializer.ts | efe078365e504dd924a43dd39d3bce60731d6ceb | 13711 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/tileMap.ts | 5b80cc0397cffc146a9f50e0a217cea403befc66 | 3080 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/layout/workstations.ts | 963de053e141e5288b381548b8164a911eaa96df | 4908 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/projection.ts | f900c4a07dcf4bd2b86bbc4700987eb6a1592e5c | 2739 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/bubble-permission.json | e786a37efd5d164e69ad4f8dcee167a2ddbe2561 | 938 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/bubble-pet.json | 38c16e67cedad17b8e3eeef946b02b6907c1be67 | 900 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/bubble-waiting.json | b496c662ef0a87f7c1aa410be6ae913bea6950df | 933 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/carpetTiles.ts | cdaf0661f08e776409df866a3627d3cc7a71958e | 12574 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/index.ts | 2d9205cced60816116a4ca534c7439c42bee8097 | 414 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/petSpriteData.ts | 4608ecb0009397f69198bb33324dc6eb35105619 | 3456 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/spriteCache.ts | 5c320f910594b653afccf1b8b645136a6e82a731 | 2399 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/sprites/spriteData.ts | 3e5906c72510c368831b3599042ca97d234d9a93 | 7941 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/toolUtils.ts | 9ca01de477ee96577daaacfcd75a5afb1db5ac51 | 1613 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/types.ts | 00f3f3dc8735bb2ffae0b78c37b4b89aa17f4bc2 | 12070 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/office/wallTiles.ts | 694f2f965be03ec1623523ece2af1dda67288c5f | 6111 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/runtime.ts | 39316cd1563dc344232832f90fb23c82dff4c0c4 | 1037 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/terminal/clipboard.ts | ef13774c9c7fda3d54b828df981e937f3f63ad66 | 2358 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/clipboardInput.ts | edc8c394ff78d57bc12e8d62ee87f431def5ae1f | 2720 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/clipboardKeys.ts | 821de1064338dce111baebeec1d9181d8aa1a47b | 3313 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/flowCopy.ts | ac2543f3fb4540c88edb0eb3b24e728be3514e86 | 3568 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/hardWrap.ts | 2103caa91dd81d3a9dfc7d17d958a1eaaaa2fadd | 1606 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/inputArea.ts | 73bd454fc7a804e8c9dff9c6eb2f9cf852988301 | 1628 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/linkProvider.ts | 2c4f54e645ade5ed26ba27743806144b996480a8 | 1115 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/selectionMath.ts | 41afffed8a2481e631d6c968d5cd57427083b5c6 | 7126 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/selectionOverlay.ts | 8235a87cec24c281d6e4e7ca972dd0320033587d | 5433 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/terminalClient.ts | 55336df6e5d593008c0f1c7d34c8d0d0c5ae3d10 | 6134 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/terminalDom.ts | 9ce6295d74eea875f74305520e617cf65cb674c4 | 2155 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/terminalLinks.ts | aa5a2f0ddb166fe448d49bec5bb9be4accea0d9f | 6409 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/touchGesture.ts | 245b94b166fe78d2eb8098ac58a01bd9b2a36e47 | 20336 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/terminal/touchInput.ts | 9d35d41762973ef5fbf369803a1d1c2df00617b4 | 10570 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/testHooks.ts | e2f7fe210be54bd6448c7735b0baaf29dfe9ca32 | 12733 | FETCHED / REVIEWED REPORTED — exact coverage/cross-references to reconcile — SHA RECONCILED GOV-WEBVIEW-004; historical per-file line coverage not evidenced; direct verification remains |
| webview-ui/src/touch/touchPrimitives.ts | d2d917653ed39a92d8b49e85e0e18a69c69158ae | 1268 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/touch/trackpad.ts | 7098d01522eb4f56c22f3d6d80414ab824ace778 | 4916 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/index.ts | 066cb0b06dd1c9a00c74dda27a21fe0c49e1b14a | 2488 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/onReconnect.ts | 030ae419587d5f3b69b78d74cf295811087e2bcd | 598 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/postMessageTransport.ts | f9b622f6bc28eb18b3bb7131e10c596ad7e68acd | 1520 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/reconnectBackoff.ts | 44dc5aac6b52a73f35700509036a248396e3cc20 | 479 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/serverToken.ts | 0ab778876c6f541005f2c38b11206d833a6468c4 | 1252 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/types.ts | e24da35d139a45ca7f8861b2a8e4547152f1a328 | 158 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/transport/webSocketTransport.ts | 33008b95327f9e2a18e23aaf0f2060416c95aba9 | 4560 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/usage/PixelHeart.tsx | 218aa0fdf9083a829e467a43e6e14819e4f85acd | 2190 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/usage/UsageBars.tsx | c2d948a944f791dcb84a2f2dc2a941aa1e0e975c | 5745 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/usage/usageBarView.ts | b85fb9e51a42d4c590f18693a3bad20e1db113a0 | 3104 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/usage/usageStore.ts | 7330bf9bd856d32dfef20633f7dc391b4a2644e2 | 2842 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/src/usage/useUsageBridge.ts | 270909fcba42e615b95d6229b66d61b1224f9072 | 2168 | REVIEWED — prior subsystem audit; cross-module follow-up remains |
| webview-ui/test/build-subpath.test.ts | 6531f84f343232b656cd9265d701a478babd4277 | 4933 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-003; not executed |
| webview-ui/test/cardBar.test.ts | 39e3b000b1aef67abd7f0f342330af44437c0944 | 2615 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/castHistory.test.ts | 3de1c964733ca76f8472906d15572f1d4313ef85 | 2181 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/castSync.test.ts | 2a0c507d8173811995c8b6578079d29b20acdc04 | 9866 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-002; not executed |
| webview-ui/test/characterRenderer.test.ts | 618af4fec0b4968feae3bddee3f4e16d64d42da4 | 11253 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-002; not executed |
| webview-ui/test/characterSheet.test.ts | 8e3c4de80cedafb49046afd15a6c578b1a51a04b | 7181 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/characterTemplatesVersion.test.ts | b34861e202e317340a3e6402f03602f4a6c8b8e0 | 1126 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/clipboardKeys.test.ts | 6fd711c2e492c7fa7695104c90ce18952e47373a | 4269 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/contextGauge.test.ts | a4b3cc4c4a96f3169afde059e3128af6df5924a1 | 2734 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/conversationsRecent.test.ts | 56e1c4a7d27821aec41b4c7c780217c0825f88d7 | 936 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/despawnSeat.test.ts | f9354dd17f64bde82d6990f9f77cfa75350f0557 | 3311 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/dev-assets.test.ts | 44c181de7e7f63904143d6e7ccdf3b0fc33bb0b7 | 3507 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-003; not executed |
| webview-ui/test/directoryStrip.test.ts | fdc1cc3089fc2eade4f468ee86c6c3393f570045 | 1524 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/edgeSwipe.test.ts | 01da285159b5c81c02a374dd02a5acc3658afa7e | 5852 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/editorActions.test.ts | b27ca7b3529a019a542fc0bc701d0672da662fff | 4289 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/editorState.test.ts | e2122f1b8c0da2e0390aef840a518602e0235ab0 | 2319 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/existingAgents.test.ts | 11914967532cda8fa8bc62e84613e5f56bfdb80a | 7099 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-002; not executed |
| webview-ui/test/flowCopy.test.ts | 4d8c35241b72d6516dd3c101288b577631a6b017 | 6567 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/greeter.test.ts | 116ae19c1446e888eb763214d13249f48dec357f | 8863 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/greeterCharacter.test.ts | b164d53b18a95d88198afbcca6db780084312b2a | 1429 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/helperFollow.test.ts | f952914e490d06cbf12331b3f98465e41d4b2064 | 5637 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/inputArea.test.ts | af99bc832e212079e6865c1c20b3924ac5b7f83d | 1330 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/introBubbleGeometry.test.ts | bd969a0f7869f787f2a3833be47eec3bf08632cf | 7547 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/introTour.test.ts | 86fab0a9080ba5336818bcd014b81d18f2505642 | 8952 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/keyboardViewport.test.ts | c84215e44d8a718b1ea82cef06186777870b3bed | 3156 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/launchLatch.test.ts | b4eda6652c842ddee7e68a75f68469dd490d2e93 | 1069 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/launchMenus.test.ts | 958bcd3ea604559a65e47b5f3b9acd0762fee037 | 2753 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/layoutSerializer.test.ts | bdc28518315db12c7c0981701baff3d52ff67a89 | 5885 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/musicComposer.test.ts | ad5eb57cda316b2bc0dd5f99a332840e5246f8b4 | 7603 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/officeBlink.test.ts | ad40215944dbb2e5bec43e638130cfc28d5f040c | 2469 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/officeBlinkSprites.test.ts | 6b8b14673e493221060913b8d04f2cb992debfc5 | 2750 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/officeCanvasCursor.test.ts | 5bde5502619ceede4d3e6603a47885eada49151a | 6972 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/onReconnect.test.ts | 344ffa982c77b11cba8529942ce55cc18febff83 | 1828 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-002; not executed |
| webview-ui/test/petEntity.test.ts | 0897047475e5b1cf3d1595b669d66962b983a34c | 13187 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/pieceItems.test.ts | 31a96bd13dee646e00dd70346f4679b58f8f53e1 | 1224 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/pixelFont.test.ts | 18531e885497d230b876dd6cf4bdc72ac1c5b158 | 2446 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/pixelGrid.test.ts | 9ad8c421ba795f37e127eb8f8d276ecd57aabc09 | 1313 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/pixelSnap.test.ts | e6e9e8a165b37c379832ebfecbb0473f5b6cdf33 | 1289 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/playerFit.test.ts | ffafbaecc2feac7a724bd171bb8d86f604beba9e | 2909 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/playerSheet.test.ts | 2142af5f2536c21bf3bfce214bd10b6454471cbf | 3845 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/recentCharacters.test.ts | 061396d8d16006d6fae1a4e8f045539babd657dd | 557 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/seatedIdle.test.ts | a82c8f9bb099e7ba81dc1abcd8482043658d844d | 4210 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/selectionButtons.test.ts | b58db130650c087b139bc4e833f594bfc78edae6 | 2640 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/selectionMath.test.ts | 110632affff46ab5a788e99770f191b83a9b7b86 | 7087 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/slideTransitions.test.ts | 43beb2021446681e7d2954107aef3da3a3cf319d | 1911 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/subagentResync.test.ts | 186ac30aa58919e0affda0b8a25ba849bb3825a6 | 3875 | TEST-INVENTORIED — full test source and assertions inspected in GOV-WEBVIEW-002; not executed |
| webview-ui/test/teammateSeating.test.ts | 4f84bd6b7ebea60bdad715d0ee9842198fca90c0 | 3900 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/terminalLinks.test.ts | 9454e202fc3d1a8c781824faac6769c7993b2a20 | 8848 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/touchGesture.test.ts | fce4e63d43ace5db03782479e4a7980f3f0ca94f | 16570 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/trackpad.test.ts | 8bf930c4a4bd3545c634fc62b0579bd60c7c7bb2 | 6320 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/usageBarView.test.ts | 6259843f93d04290a98b705a2093caa5c10065ea | 2773 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/usageStore.test.ts | 301c057bd160e6b02762a940c0ed6d54da3b5677 | 2562 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/wardrobeLabels.test.ts | 756676f90fa1195937dd4dd7db6fcbdf041481ca | 1723 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/test/workstations.test.ts | 331448e5cdc5ce20f27f682cc433e19c80f417c7 | 6992 | FETCHED / INVENTORIED — assertions catalogued; not executed |
| webview-ui/tsconfig.app.json | 23fe8cc3ddc9a92928117665db2ea1432b1605d7 | 767 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/tsconfig.json | 1ffef600d959ec9e396d5a260bd3f5b927b2cef8 | 119 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/tsconfig.node.json | a98025607ec565eca8fa09f18336ba2ced07939b | 834 | REVIEWED — root/package/build/config file content read; no dynamic execution |
| webview-ui/vite.config.ts | b47531773bf6fea57fe6d46a6291133371aa56ea | 4848 | REVIEWED — full content re-fetched and statically reviewed in GOV-WEBVIEW-003; watcher add/unlink behavior remains open |
| webview-ui/vitest.config.ts | 3ed45c68d601468600716a0fd69b8cdb12739f16 | 1077 | REVIEWED — root/package/build/config file content read; no dynamic execution |

# Architecture and ADR Reconciliation Record

**Status:** PARTIAL — current implementation compared with the Hybrid LLM Engine direction and all three repository ADRs at source snapshot `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`.

## Ownership

- [Architecture/ADR reconciliation](../../ARCHITECTURE-ADR-RECONCILIATION.md)
- [Directional design note](../../../architecture/K41-HYBRID-LLM-ENGINE-DIRECTION.md)
- `docs/adr/0001` consent revision semantics
- `docs/adr/0002` glossary-led wire vocabulary
- `docs/adr/0003` palette/repeat visual identity and persistence compatibility

## Reconciliation status

| Record | Status | Summary |
|---|---|---|
| Hybrid LLM Engine direction | PARTIAL / FUTURE | Directional only; no generic Main Agent, LLM worker scheduler, C engine, Android client or measured performance target identified in current tree |
| ADR-0001 | SOURCE-VERIFIED ALIGNMENT (static) | Immediate consent send, tri-state durable record, process-wide serialization and absolute revision semantics found in server/webview code |
| ADR-0002 | SOURCE-VERIFIED ALIGNMENT (static) | Directory vocabulary is used in glossary and AsyncAPI/generated wire contract; legacy persisted folderName remains a read migration fallback |
| ADR-0003 | PARTIAL SOURCE-VERIFIED ALIGNMENT | repeat is emitted, persisted and rendered; legacy hue fields migrate on restore; full characterSpritesLoaded path not fully reconciled here |

## Unresolved design decisions

Preserve all 12 open questions in the directional note: worker lifecycle, model topology, Main Agent runtime, concurrency, memory, event/backpressure, task lifecycle, persistence, tool isolation, networking/security, client UI and performance targets. Existing Pixel Agents features must not be treated as implicit decisions for the new engine.

No duplicate finding IDs created. No tests, builds, benchmarks, network calls or runtime experiments executed.

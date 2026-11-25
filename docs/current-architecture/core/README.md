# Core — Current Architecture

**Status:** AUDITED — source inventory, static review and documentation reverified for the recorded snapshot; cross-layer/runtime verification remains partial.

The `core/` directory is a shared, host-neutral layer. It contains contracts, protocol definitions, pure domain utilities, character/profile validation and migration, terminal framing, and an asset decoding/build pipeline. It is not the LLM orchestration engine and contains no AI model execution logic.

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Protocol](PROTOCOL.md)
- [Dependencies](DEPENDENCIES.md)
- [Governance audit record](../governance/modules/core.md)
- [Evidence log](../governance/EVIDENCE-LOG.md#2026-11-25--gov-core-001-core-source-and-documentation-re-verification)

Audit baseline: branch `docs/hybrid-llm-engine-direction`, source snapshot rooted at `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1`. Reverified at branch tip `13354f1a74b75b9ab3d8982544414134b1940ae3`; all 28 core blob SHAs match. Inventory: 1 AsyncAPI YAML + 27 TypeScript files; recursive Git tree reports `truncated: false`. No application source was changed.

**Status vocabulary:** VERIFIED means directly established from source/configuration; INFERRED means strongly indicated but not proven by a runtime trace; OPEN means further evidence is required. A source audit does not imply that tests or builds were executed.

The repository-wide audit process is governed by [Audit Governance](../governance/README.md). Use its register and evidence log to continue without repeating completed source reads.

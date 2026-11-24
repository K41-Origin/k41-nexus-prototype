# Core — Current Architecture

**Status:** AUDITED (source inventory and contract review); runtime-consumer verification remains partial.

The `core/` directory is a shared, host-neutral layer. It contains contracts, protocol definitions, pure domain utilities, character/profile validation and migration, terminal framing, and an asset decoding/build pipeline. It is not the LLM orchestration engine and contains no AI model execution logic.

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Protocol](PROTOCOL.md)
- [Dependencies](DEPENDENCIES.md)

Audit baseline: branch `docs/hybrid-llm-engine-direction`, tree rooted at commit `da006d70c6ee45c5ae8179ed1f5ef767690a95ba`. Inventory is based on the complete recursive `core/` tree (26 tracked files: one AsyncAPI specification and 25 TypeScript files). No application source was changed.

**Status vocabulary:** VERIFIED means directly established from source/configuration; INFERRED means strongly indicated but not proven by a runtime trace; OPEN means further evidence is required. A source audit does not imply that tests or builds were executed.

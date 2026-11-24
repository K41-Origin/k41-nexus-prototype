# Core — Current Architecture

**Status:** AUDITED (source inventory and contract review); runtime-consumer verification remains partial.

The `core/` directory is a shared, host-neutral layer. It contains contracts, protocol definitions, pure domain utilities, character/profile validation and migration, terminal framing, and an asset decoding/build pipeline. It is not the LLM orchestration engine and contains no AI model execution logic.

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Protocol](PROTOCOL.md)
- [Dependencies](DEPENDENCIES.md)

Audit baseline: branch `docs/hybrid-llm-engine-direction`, tree rooted at commit `d0d5de756bb531d4d1fd1d97ee22f092d9feaaf1`. Inventory is based on the complete recursive `core/` tree (28 tracked files: one AsyncAPI specification and 27 TypeScript files; Git tree reports `truncated: false`). No application source was changed.

**Status vocabulary:** VERIFIED means directly established from source/configuration; INFERRED means strongly indicated but not proven by a runtime trace; OPEN means further evidence is required. A source audit does not imply that tests or builds were executed.


## Second-pass verification

A second recursive Git tree inventory reported `truncated: false`, with 28 tracked files total: 1 AsyncAPI YAML specification and 27 TypeScript files. All 28 files were individually fetched successfully. The earlier inventory count of 26 was a counting error and has been corrected in the file reference.

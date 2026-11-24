# Server — Current Architecture

**Status:** DEEP STATIC SOURCE AUDIT DOCUMENTED — runtime verification pending

**Coverage:** 74/74 runtime source files reviewed; 57/57 test/support files fetched and suite cases inventoried; 4/4 root support/config files reviewed. See [Deep Audit Findings](DEEP-AUDIT.md).  
**Audited baseline:** `89851b12b968fcf30288f1edf4315e6377d51325`  
**Inventory:** 135 tracked files (132 TypeScript, 2 JSON, 1 HTTP); includes 76 source/config/support files and 59 test/support files.

The server is the Node.js host/runtime for Pixel Agents. It ingests provider hook events and transcript records, maintains observed agent/session state, persists shared and adapter-scoped state, serves HTTP/WebSocket interfaces, and optionally owns standalone PTY terminals. It is **not** a general-purpose LLM inference engine or model gateway.

## Documents

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Runtime lifecycle](RUNTIME-LIFECYCLE.md)
- [Providers](PROVIDERS.md)
- [Persistence](PERSISTENCE.md)
- [HTTP and WebSocket](HTTP-WEBSOCKET.md)
- [Dependencies](DEPENDENCIES.md)

## Audit scope and limits

The tracked-file inventory was taken from the recursive Git tree at the audited commit (`truncated: false`). Source responsibilities and major call paths were statically inspected. This is not a runtime-complete audit: tests, builds, live sockets, real Claude Code hook delivery, native PTY behavior and cross-platform behavior have not been executed as part of this pass. Test files are inventoried in the file reference; their existence is not evidence that they pass.

No application source code was changed by this documentation audit.

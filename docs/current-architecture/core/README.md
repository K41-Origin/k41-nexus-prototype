# Core — Current Architecture

**Status:** NOT AUDITED (module-level review in progress)

The `core/` layer contains shared contracts and host-neutral logic. The repository contributor reference states that core must not depend on other application layers.

- [Module overview](MODULE-OVERVIEW.md)
- [File reference](FILE-REFERENCE.md)
- [Protocol](PROTOCOL.md)
- [Dependencies](DEPENDENCIES.md)

Do not add server, UI, or editor-specific implementation details here unless the current source actually places them in core.

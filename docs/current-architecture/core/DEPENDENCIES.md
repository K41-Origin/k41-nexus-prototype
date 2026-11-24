# Core Dependencies

**Status:** NOT AUDITED

Record:
- direct package dependencies;
- internal imports;
- environment/runtime assumptions;
- generated-code relationships;
- consumers in server, UI and adapters.

The documented architectural rule says `core/` depends on no application layer. Verify actual imports and build enforcement before marking this invariant VERIFIED.

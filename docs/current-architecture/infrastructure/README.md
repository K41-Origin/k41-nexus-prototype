# Infrastructure — Current Architecture

**Status:** NOT AUDITED as a consolidated infrastructure scope. Individual evidence exists for selected CI/release workflows and scripts, but build-system, dependency and testing infrastructure have not received a complete integrated audit.

## Document ownership

- [Build system](BUILD-SYSTEM.md) — package/workspace scripts, TypeScript projects, bundlers, generated protocol and artifacts.
- [Testing](TESTING.md) — test frameworks, configurations, test-to-module mapping and execution evidence.
- [CI/CD](CI-CD.md) — workflow configuration and integration with build/release tooling.
- [Dependencies](DEPENDENCIES.md) — production/development/optional/platform dependency classification.
- [Scripts static audit](../SCRIPTS-AUDIT.md) — 14/14 files under `scripts/`, BUILD-001–BUILD-007.
- [GitHub CI/release audit](../github-ci/DEEP-AUDIT.md) — 10/10 `.github/` files, BUILD-008–BUILD-013.

The scripts and `.github/` audits are completed scoped audits; they do not imply that the broader infrastructure documents are complete. See [repository audit register](../governance/AUDIT-REGISTER.md) for consolidated status.

# CI/CD

**Status:** PARTIAL — workflow-to-build/test/package/release integration statically mapped. Runtime runs, repository/organization settings and registry outcomes remain OPEN.

## Source snapshot

The source baseline for this integration pass is `security/remediate-build-008-009@86d3b1dbddae7605e5e2092886c12982f8e4bf58`, based on `main@56f354f2808ae5aba10b0e1d5892bd9fa7ca095b`. The only two changed source blobs from that base are the CI and release workflows documented below.

## CI workflow integration

`.github/workflows/ci.yml` blob `8dc8cdf30e2cb1857708a8bd601807c08df34368`:
- Main CI job installs npm workspaces with `npm ci`; validates AsyncAPI; regenerates and checks generated-message drift; checks E2E inventory drift; runs typecheck, lint, webview tests, format check and advisory Knip; builds extension and webview; verifies npm package contract/artifact; runs server tests after build; and runs npm audit.
- Separate Node 20 package smoke job runs package contract and installed npm package verification.
- Playwright E2E runs on Ubuntu, macOS and Windows, three shards per OS (nine jobs), one worker per shard.
- `required-checks` aggregates the main CI, Node 20 package smoke and E2E matrix. Workflow configuration exists; actual branch protection/organization enforcement is not verified (BUILD-010).
- The PR Vercel preview jobs were removed in the remediation branch; the preview feature is suspended to remove the privileged PR execution path (BUILD-008 remains OPEN pending PR negative test and merge).

## Release workflow integration

`.github/workflows/publish-extension.yml` blob `0b408f04d6b7c47d1405b9259d257fd21bdcb15d`:
- `build-and-verify` is read-only, checkout credentials are not persisted, and it runs typecheck/lint/tests, VSIX dry-run/package verification and npm package verification. npm `prepack`/package scripts run only in this unprivileged build job.
- It stages the VSIX, npm tarball and metadata with source commit/ref and uploads a run-scoped artifact.
- `publish-vsix` and `publish-npm` run only for `release.published`, consume the artifact without checkout or repository source execution, and receive only the permissions/secrets they need.
- `id-token: write` is confined to `publish-npm`; that job installs pinned npm with lifecycle scripts disabled, checks artifact integrity and registry idempotency, then publishes the tarball with provenance.
- Manual dispatch is verification-only and rejects `dry_run=false`. BUILD-009 remains OPEN pending manual negative tests, release positive rehearsal and settings evidence.
- Targeted action SHA pins do not close repository-wide BUILD-011; BUILD-012/013 remain separate.

## External configuration dependencies

Not source-verifiable from the repository tree:
- GitHub branch protection/rulesets and required-check enforcement;
- Actions default token permissions and organization policy;
- `npm-publish` Environment reviewers and allowed deployment refs;
- npm trusted publisher repository/workflow/Environment tuple;
- VSCE_PAT/Open VSX token scopes and Vercel secret history/scope;
- actual GitHub run/job permissions, artifact digest and registry provenance.

These remain explicit OPEN verification gates. A workflow's YAML `environment: npm-publish` declaration does not configure its protection rules.

## Existing audit ownership

- [GitHub CI/release deep audit](../github-ci/DEEP-AUDIT.md) owns BUILD-008–BUILD-013 and the remediation verification plan.
- [Scripts audit](../SCRIPTS-AUDIT.md) owns BUILD-001–BUILD-007.
- [Build system](BUILD-SYSTEM.md) owns build/package command and output graph.
- [Testing](TESTING.md) owns suite/configuration mapping.

No workflow was dispatched; no tests, build, package, publish, OIDC request or settings mutation was performed.

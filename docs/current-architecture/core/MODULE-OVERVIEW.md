# Core Module Overview

**Status:** NOT AUDITED

## Responsibility
Record the actual responsibilities and boundaries of `core/`.

## Internal areas
To be populated from source inspection: protocol/messages, schemas, provider contracts, transport, persistence/adapter contracts, shared utilities, asset logic.

## Public contracts
List exported types, interfaces, functions and generated contracts with source references.

## Runtime behavior
State whether each component is pure/shared logic or has runtime side effects. Verify from source.

## Constraints
Record dependency rules and generated-file ownership from repository instructions and build scripts.

## Open questions
- Which exports are consumed by each host layer?
- Are any core modules unexpectedly environment-specific?

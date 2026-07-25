# ADR-001: Offline-first core

- Status: Accepted
- Date: 2026-07-25
- Owners: CEO and CTO Office

## Context

KinRaiDee manages household pantry, cooking, history, and future shopping data. These workflows should remain available during poor connectivity, should respond quickly, and should preserve user trust in locally entered information.

A cloud-first architecture would create unnecessary dependency for core kitchen actions and would increase privacy, availability, and cost risks before the product has validated its essential workflow.

## Decision

Core KinRaiDee workflows will be designed to work offline using local persistence.

The offline core includes:

- pantry management;
- local recipe access for installed content;
- recommendation based on local data;
- serving adjustment;
- cooking completion and pantry deduction;
- cooking history and correction;
- shopping-list management.

Cloud services may later provide sync, sharing, remote content, backup, or hosted AI, but those services must enhance the local product rather than replace it.

## Consequences

### Positive

- faster and more reliable everyday usage;
- useful product during connectivity failures;
- stronger privacy posture;
- lower early infrastructure dependency;
- a natural path toward optional on-device AI.

### Negative

- eventual sync will require explicit conflict resolution;
- migrations and local storage integrity become important responsibilities;
- remote analytics may be incomplete unless designed carefully;
- content availability must be managed through installed or cached data.

## Guardrails

- New core features must identify their offline behaviour.
- Cloud-only dependencies require an RFC and CTO approval.
- UI must communicate when an optional online enhancement is unavailable.
- Local data export and migration should remain future-compatible.

## Alternatives considered

### Cloud-first

Rejected for the initial product because it creates avoidable availability and privacy dependency.

### Online-only AI application

Rejected because AI without trusted local pantry workflows would not solve the core kitchen problem.

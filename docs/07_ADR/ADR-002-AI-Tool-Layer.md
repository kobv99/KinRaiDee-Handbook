# ADR-002: AI accesses product data through tools

- Status: Accepted
- Date: 2026-07-25
- Owners: CTO Office

## Context

KinRaiDee is expected to add optional AI capabilities in the future. Pantry, history, shopping, nutrition, and budget data may be stored locally today and synchronised or migrated later.

Allowing an AI model to access Hive or another persistence layer directly would tightly couple model behaviour to storage details, weaken validation boundaries, and make silent data mutation more difficult to control.

## Decision

AI capabilities will access KinRaiDee data and actions only through explicit domain tools or interfaces.

Potential tools include:

- Inventory Tool;
- Recipe Tool;
- History Tool;
- Shopping Tool;
- Nutrition Tool;
- Expiration Tool;
- Budget Tool.

Read and write permissions must be explicit. Mutating actions require deterministic validation and product-level user confirmation.

## Consequences

### Positive

- AI remains independent of storage technology;
- permissions and validation are enforceable;
- tool behaviour can be unit tested;
- local and cloud models can use the same contracts;
- model replacement becomes less disruptive;
- household data mutations remain auditable.

### Negative

- tool contracts require design and maintenance;
- additional translation code is needed between model intent and domain operations;
- early prototypes may take longer than direct database access.

## Guardrails

- AI must not receive unrestricted storage handles.
- Tools must expose only the minimum data and action scope required.
- Model output is not authoritative until validated by domain logic.
- Mutations must be idempotent or protected against repeated execution where practical.
- Sensitive data exposure must be documented in the relevant RFC.

## Alternatives considered

### Direct database access

Rejected because it bypasses domain rules and couples AI to persistence.

### Prompt-only context injection

Useful for read-only prototypes but insufficient as the long-term architecture for reliable actions and permissions.

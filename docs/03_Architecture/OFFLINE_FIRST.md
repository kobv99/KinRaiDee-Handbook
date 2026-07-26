# Offline-First Operating Model

KinRaiDee must remain useful without an internet connection.

## Required behaviour

- Pantry, recipe, recommendation, cooking, history, and shopping-core workflows operate locally.
- Local persistence is authoritative for the active device unless a future synchronisation design explicitly defines conflict handling.
- Network, AI, analytics, and cloud failures must not block core kitchen actions.
- The application must not require login merely to access local core capabilities.

## Future cloud boundary

Cloud services may provide backup, synchronisation, shared households, or enhanced AI. They are optional extensions.

Before cloud synchronisation is implemented, an ADR must define:

- identity and device ownership;
- local and remote source-of-truth rules;
- conflict resolution;
- deletion semantics;
- migration and rollback;
- privacy and security boundaries;
- offline queue behaviour.

## Failure design

- Local writes should complete independently of remote availability.
- Deferred remote operations must be observable and retryable.
- Users must not lose local work because a future sync attempt fails.
- Destructive conflict resolution requires explicit product rules.

See ADR-001 for the accepted decision.

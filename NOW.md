# Now

_Last updated: 2026-07-26_

This document answers one question: **What should the engineering team work on now?**

## Current priority

**Architecture readiness stabilisation before Shopping Foundation**

Shopping readiness verdict: **NOT READY**

Do not implement Shopping until the required gates in `docs/02_Architecture/ARCHITECTURE_REVIEW.md` are complete.

## Work next

1. Open and review the current source branch `feature/cooking-history-4.8.1`; establish the approved base SHA.
2. Fix the wall-clock-dependent expiry test and make the full test suite green.
3. Apply the Dart formatter in a dedicated behavior-neutral source commit and enforce it in CI.
4. Introduce one application transaction boundary for cooking completion, History adjustment/cancel, and undo.
5. Add failure-injection, restart-recovery, stale-transaction, and partial multi-item undo tests.
6. Add repository contracts for Cooking History and persisted recipe preferences; remove direct persistence from presentation.
7. Choose one completion-feedback owner and test completion -> Pantry -> undo.
8. Approve canonical ingredient identity, Pantry lot identity, unit normalization, schema migration, and Shopping lifecycle decisions.
9. Accept or replace proposed ADR-003, ADR-004, and ADR-005.
10. Rerun the architecture readiness audit.

## Shopping start gate

Shopping Foundation may start only when:

- the current source branch is reviewed and the base is explicit;
- Pantry + History mutations have atomic or proven compensating behavior;
- transaction preconditions and multi-item undo behavior are defined and tested;
- canonical ingredient and quantity/unit contracts are accepted;
- presentation has no direct Hive/`StorageService` access;
- Shopping specification decisions are closed;
- analyze, formatting, full tests, and failure-path tests are green.

## Do not start

- Shopping implementation while the verdict is **NOT READY**
- Nutrition implementation
- Meal planner implementation
- Authentication
- Cloud synchronisation
- Family sharing
- Vision AI
- Direct generative-AI database access

## Stop conditions

Stop and request clarification when:

- the handbook conflicts with source code;
- business rules are ambiguous;
- implementation requires bypassing repository boundaries;
- transaction safety cannot be guaranteed;
- the approved source base is unclear;
- canonical identity or unit semantics are unresolved;
- a full test or formatting gate is red;
- the requested work expands beyond Shopping Foundation without approval.

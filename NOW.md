# Now

_Last updated: 2026-07-26_

This document answers one question: **What should the engineering team work on now?**

## Current priority

**Shopping Foundation**

## Before implementation

1. Read `CURRENT_STATE.md`.
2. Verify the latest application branch and commit in `kobv99/KinRaiDee`.
3. Run existing tests locally.
4. Confirm cooking-history cancellation and pantry restoration remain correct.
5. Read the relevant architecture rules and accepted ADRs.
6. Produce or approve a Shopping feature specification before coding.

## Work next

- Define the Shopping domain boundary.
- Define shopping-list item states and lifecycle.
- Define how missing recipe ingredients become shopping candidates.
- Define repository interfaces and persistence ownership.
- Define acceptance criteria and transaction rules.
- Implement only after CTO review of the specification.

## Do not start

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
- the requested work expands beyond Shopping Foundation without approval.

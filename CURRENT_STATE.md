# Current State

_Last updated: 2026-07-26_

This document describes the current product and engineering state. Update it whenever the implementation reality changes.

## Repositories

- Source code: `kobv99/KinRaiDee`
- Handbook: `kobv99/KinRaiDee-Handbook`
- Mobile app path: `apps/mobile`
- GitHub is the source of truth.

## Current product stage

KinRaiDee has a functional offline-first cooking workflow. Architecture audit `d8631869` found that the current application is **NOT READY** for Shopping Foundation. The active stage is architecture-readiness stabilisation, not Shopping implementation.

## Implemented capabilities

- Pantry management
- Recipe management
- Smart recommendations
- Recipe serving adjustment
- Use-soon recommendations
- Recipe coverage analysis
- Pantry deduction after cooking
- Cooking history
- Cooking-history cancellation and pantry restoration

## Verified source state

- Audited branch: `feature/cooking-history-4.8.1`
- Audited commit: `d8631869bb7e0eb18a9cc136189006212f8737dd`
- Commit message: `fix(history): retain returned amount when cancelling an adjusted record`
- Default branch: `main` at `b27e1762c5eada0a9753796c7d11d0468534d801`
- Branch relationship at audit: the audited branch is 176 commits ahead of `main`.
- Open source pull request for the audited branch at audit: none found.

Starting Shopping from `main` would omit the current application work. Starting from the unreviewed feature branch would inherit an unstable base. The source branch must be reviewed and reconciled before a Shopping branch is created.

## Current milestone

**Architecture readiness stabilisation before Shopping Foundation**

Shopping remains the next product milestone, but implementation is gated by the architecture findings in:

- `docs/02_Architecture/ARCHITECTURE_REVIEW.md`
- `docs/02_Architecture/DEPENDENCY_GRAPH.md`
- `docs/02_Architecture/DATA_MODEL_REVIEW.md`
- `docs/08_CTO/TECH_DEBT.md`
- `docs/08_CTO/REFACTOR_PROPOSAL.md`
- `docs/08_CTO/RISK_REGISTER.md`

## Current focus

1. Review and reconcile `feature/cooking-history-4.8.1` into an approved source baseline.
2. Make Pantry + History completion, adjustment, cancel, and undo atomic or safely compensating.
3. Validate transaction preconditions and eliminate partial multi-item undo/history divergence.
4. Approve repository, transaction, canonical ingredient identity, and unit contracts.
5. Make analysis, formatting, full tests, and critical failure-path tests green.
6. Rerun the readiness gate before beginning Shopping.

## Audit validation

Executed from `apps/mobile` at `d8631869`:

- `flutter analyze --no-pub`: **PASS**, no issues in 6.4 seconds.
- Full `flutter test --no-pub --reporter expanded`: **FAIL**, 58 passed and 1 time-dependent expiry test failed.
- Pantry/History/Cancel/Undo targeted tests: **PASS**, 12 tests.
- Recipe/Deduction/Serving targeted tests: **FAIL**, 11 passed and the same expiry test failed.
- Recommendation targeted tests: **PASS**, 22 tests.
- Dart formatting verification: **FAIL**, 40 of 87 files require formatting.
- Measured line coverage: 41%; domain services are strong, while `RecipeDetailPage`, `CookingHistoryPage`, and storage workflow coverage is low.
- `flutter pub get`: dependencies resolved, but the command exited non-zero because Windows Developer Mode/symlink support is disabled on the audit host.

## Known risks

- Pantry is persisted before Cooking History with no rollback or durable recovery journal.
- Transaction application does not validate every current quantity, unit, ID, or duplicate change before mutation.
- Quick undo can partially restore a multi-lot transaction while History remains completed.
- Pantry, Recipe, and the Pantry picker do not share canonical ingredient identities.
- Units are free-text and insufficiently normalized for Shopping aggregation.
- Presentation providers access `StorageService` directly despite repository rules.
- The full test and formatting gates are red.
- Mutable Hive payloads have no explicit schema or migration version.
- The current application branch is not established on the default branch.

See `docs/08_CTO/RISK_REGISTER.md` for probability, impact, mitigation, owner, trigger, and status.

## Planned capabilities

- Shopping Foundation
- Nutrition
- Meal planning
- AI Chef and tool-based agents
- Vision-assisted ingredient capture
- Optional cloud synchronisation
- Family sharing

## Status rule

This file must reflect verified reality. When handbook and source conflict, stop implementation, inspect the source repository, and update this document before continuing.

No production code or product behaviour was changed by the architecture audit.

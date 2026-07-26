# Current State

_Last updated: 2026-07-26_

This document describes the verified product and engineering baseline after the architecture audit, Transaction Engine, Canonical Ingredient System, and integration stabilization.

## Repositories

- Source code: `kobv99/KinRaiDee`
- Handbook: `kobv99/KinRaiDee-Handbook`
- Mobile app path: `apps/mobile`
- GitHub `main` is the source of truth.

## Current product stage

KinRaiDee has a stable offline-first Pantry, Recipe, Recommendation, and Cooking History baseline. The architecture foundation is **READY WITH MINOR DEFERRED WORK** for Shopping Foundation, subject to explicit CTO approval to begin the Shopping sprint.

Shopping Foundation has not started.

## Integrated baseline

| Work | Pull request | Merge commit |
|---|---:|---|
| Architecture Audit + RFC-0003 | `KinRaiDee-Handbook#2` | `26275eda86959c1ed9b469e06e2dcbff5856f599` |
| Sprint S-001 Transaction Engine | `KinRaiDee#1` | `78be1dd07c99af98606a7f1a0937d75ef9fcdfb6` |
| Sprint S-002 Canonical Ingredient System | `KinRaiDee#2` | `77d0953ba0ccae976ccca678e4bd15652e11572b` |
| Integration persistence-boundary stabilization | `KinRaiDee#3` | `6bba09275bedf76944779f59a28042ce11727d11` |

Source `main` was verified identical to `6bba09275bedf76944779f59a28042ce11727d11` after the final source merge.

## Implemented capabilities

- Pantry management
- Recipe management and serving adjustment
- Smart and use-soon recommendations
- Pantry deduction after cooking
- Cooking History adjustment and cancellation
- Durable, revisioned Pantry + History transaction envelope
- Crash-safe transaction journal and startup recovery
- Idempotent commit, retry, undo, and cancel
- All-or-nothing multi-item transactions and quick undo
- Canonical ingredient registry, aliases, localized names, and deterministic unknown identities
- Deterministic unit conversion and rounding contract
- Automatic, idempotent Pantry and Cooking History canonical migration
- Riverpod publication of inventory state only after durable commit
- Repository boundary between presentation and local persistence

## Validation

Executed from `apps/mobile` against the final source tree:

- `dart format --output=none --set-exit-if-changed .`: **PASS**, 115 files checked and 0 changed.
- `flutter analyze`: **PASS**, 0 issues.
- `flutter test --coverage`: **PASS**, 108 tests.
- Line coverage: **80.77%** (`4,188 / 5,185`).
- Targeted transaction, recovery, migration, History, and recipe-persistence tests: **PASS**, 30 tests.
- Presentation-layer scan for Hive or `StorageService`: **PASS**, 0 findings.

## Migration verification

- Legacy Pantry data is wrapped in a checksummed revision-zero envelope with a legacy backup.
- Pantry and Cooking History receive canonical IDs without changing user-entered quantities or display data.
- Unknown ingredients and units are preserved and reported with deterministic identifiers.
- Re-running canonical migration is non-mutating.
- Canonical migration commits through `InventoryTransactionCoordinator`; the journal and revision remain valid after migration.
- Hive close/reopen and interrupted-commit recovery tests pass.

Evidence is recorded in `SHOPPING_FOUNDATION_READINESS.md`.

## Architecture status

The P0 transaction consistency findings and P1 canonical identity, unit, clock, formatting, coverage, source-baseline, and presentation persistence blockers from the audit are resolved on `main`.

No open architecture blocker prevents the next Shopping Foundation sprint. Minor deferred engineering work remains:

- automate the validated format/analyze/test/coverage gate in CI;
- add operator-facing local-store repair/export behavior for fail-closed corruption;
- monitor whole-envelope History growth and broad provider rebuilds;
- maintain canonical catalog curation and diagnostics for unknown ingredients;
- decompose large coordinator, repository, and page files only when feature work provides a safe seam;
- bundle fonts or explicitly disable runtime font fetching for deterministic first-run offline rendering.

## Planned but not implemented

- Shopping List
- Package sizing
- Retailer identity
- Price model
- Shopping aggregation
- Purchase workflow
- Shopping UI
- Cloud synchronization
- Nutrition
- Meal planning
- AI Chef and tool-based agents
- Vision-assisted ingredient capture
- Family sharing

## Current decision

Hold implementation until the CTO approves Shopping Foundation. When approved, begin with Shopping domain contracts and tests; do not bypass the transaction engine, canonical ingredient registry, unit contract, or repository boundaries.

## Status rule

This file must reflect verified reality. When handbook and source conflict, stop implementation, inspect source `main`, and update this document before continuing.

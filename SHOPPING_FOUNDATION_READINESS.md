# Shopping Foundation Readiness

- Assessment date: 2026-07-26
- Source repository: `kobv99/KinRaiDee`
- Verified source `main`: `6bba09275bedf76944779f59a28042ce11727d11`
- Verdict: **READY WITH MINOR DEFERRED WORK**

## Executive assessment

KinRaiDee now has a stable architectural foundation for Shopping Foundation. The original P0 transaction consistency risks and the P1 canonical identity, unit, migration, test, formatting, clock, repository, and source-baseline blockers are resolved on source `main`.

No Shopping feature was implemented during integration. Shopping may begin only after explicit CTO approval and must build on the existing transaction, identity, unit, migration, and repository contracts.

## Integrated architecture status

| Foundation | Evidence | Status |
|---|---|---|
| Architecture audit and accepted RFC-0003 | Handbook PR `#2`, merge `26275eda86959c1ed9b469e06e2dcbff5856f599`; `docs/08_RFC/RFC-0003-Transaction-Safety-and-Inventory-Consistency.md` | Complete |
| Durable transaction boundary | `apps/mobile/lib/features/pantry/application/inventory_transaction_coordinator.dart`; `apps/mobile/lib/features/pantry/domain/repositories/inventory_commit_repository.dart` | Complete |
| Journal, revision, checksum, and recovery | `apps/mobile/lib/features/pantry/data/repositories/hive_inventory_commit_repository.dart`; `inventory_state_envelope.dart`; `inventory_transaction_record.dart` | Complete |
| Durable-only Riverpod publication | `apps/mobile/lib/core/providers/pantry_provider.dart`; `features/pantry/presentation/providers/cooking_history_provider.dart` | Complete |
| Canonical ingredient identity | `apps/mobile/lib/core/domain/ingredients/canonical_ingredient.dart`; `canonical_ingredient_registry.dart`; `features/recipe/data/ingredient_catalog.dart` | Complete |
| Unit contract | `apps/mobile/lib/core/domain/units/unit_contract.dart`; standard contract constructed by `UnitConversionEngine.standard()` | Complete |
| Local data migration | `apps/mobile/lib/features/pantry/application/canonical_ingredient_migration.dart`; startup orchestration in `apps/mobile/lib/main.dart` | Complete |
| Recipe and recommendation compatibility | `features/recipe/domain/services/recipe_matcher.dart`; `smart_recommendation_engine.dart`; `pantry_deduction_planner.dart` | Complete |
| Presentation persistence boundary | `features/recipe/domain/repositories/hero_selection_repository.dart`; `data/repositories/local_hero_selection_repository.dart`; durable snapshot in `cooking_history_provider.dart` | Complete |

## Merge record

Merges were performed in the approved order:

1. Handbook Architecture Audit + RFC-0003: `26275eda86959c1ed9b469e06e2dcbff5856f599`
2. Sprint S-001 Transaction Engine: `78be1dd07c99af98606a7f1a0937d75ef9fcdfb6`
3. Sprint S-002 was retargeted and rebased onto S-001 merge `78be1dd07c99af98606a7f1a0937d75ef9fcdfb6`.
4. Sprint S-002 Canonical Ingredient System: `77d0953ba0ccae976ccca678e4bd15652e11572b`
5. Integration persistence-boundary stabilization: `6bba09275bedf76944779f59a28042ce11727d11`

## Final validation

| Check | Result |
|---|---|
| `dart format --output=none --set-exit-if-changed .` | Pass: 115 files, 0 changed |
| `flutter analyze` | Pass: 0 issues |
| `flutter test --coverage` | Pass: 108 tests |
| Line coverage | 80.77% (`4,188 / 5,185`) |
| Transaction, recovery, migration, History, and recipe-persistence targets | Pass: 30 tests |
| Presentation imports or calls to Hive/`StorageService` | Pass: 0 findings |
| Source `main` compared with final merge | Identical |

The final full test run includes successful commit, validation failure, rollback, every journal interruption stage, restart recovery, duplicate commit, duplicate retry, duplicate undo, duplicate cancel, multi-item all-or-nothing undo, Pantry mutation, Cooking History adjustment/cancel, canonical identity, unit conversion, recipe compatibility, and recommendation compatibility.

## Migration verification

| Requirement | Evidence | Result |
|---|---|---|
| Existing Pantry migrates | `hive_inventory_commit_repository_test.dart` verifies legacy Pantry becomes a checksummed revision-zero snapshot with backup; `canonical_ingredient_migration_test.dart` verifies canonical mapping without quantity loss. | Pass |
| Existing Cooking History migrates | `canonical_ingredient_migration_test.dart` verifies Pantry and History canonical IDs together while preserving the original record data. | Pass |
| Unknown ingredients remain deterministic | `canonical_ingredient_registry_test.dart` verifies deterministic unknown IDs; `canonical_ingredient_migration_test.dart` verifies unknown names and units are preserved and reported. | Pass |
| Migration is idempotent | `canonical_ingredient_migration_test.dart` verifies the second migration is unchanged; `inventory_transaction_coordinator_test.dart` verifies a repeated durable migration does not advance revision. | Pass |
| Journal remains valid after migration | `InventoryTransactionCoordinator.migrateCanonicalIngredients` uses the normal commit envelope; coordinator tests verify revision and duplicate behavior, and repository tests verify journal state/checksum/recovery. | Pass |
| Restart remains safe | `hive_inventory_integration_test.dart` verifies close/reopen durability and interrupted journal finalization. | Pass |

## Resolved blockers

- Pantry and Cooking History now commit as one checksummed, revisioned envelope.
- Transaction IDs, state transitions, retries, undo, and cancel are idempotent and tested.
- Multi-item validation and undo are all-or-nothing.
- Startup recovery resolves incomplete journal entries or fails closed.
- Riverpod inventory and History state is refreshed only from a durable successful snapshot.
- Pantry, Recipe, and Recommendation share canonical ingredient IDs.
- Unit conversion and rounding are deterministic and reject invalid or incompatible conversions.
- Existing data migrates automatically, preserves unknown values, and remains idempotent.
- Expiry-sensitive behavior uses an injectable clock.
- Formatting, analyzer, full tests, and coverage gates are green.
- Presentation no longer accesses Hive or `StorageService` directly.
- The complete application is established on source `main`.

## Remaining blockers

**None for beginning Shopping Foundation after CTO approval.**

The decisions below are work within the Shopping Foundation sprint, not unresolved defects in the existing architecture:

- Shopping item and list lifecycle
- Package sizing
- Retailer identity
- Price model
- Shopping aggregation
- Purchase workflow
- Shopping UI

Cloud synchronization is explicitly deferred.

## Open risks and minor deferred work

| Risk | Impact | Mitigation | Owner |
|---|---|---|---|
| No repository CI workflow currently enforces the green local gate. | A future branch could regress format, analysis, tests, or coverage. | Add CI for format, analyze, tests, and an 80% coverage floor before the first Shopping merge. | Repository Maintainer |
| Coverage is only 0.77 percentage points above the required floor. | Small untested additions can drop the gate below 80%. | Require Shopping tests in the same PR and monitor changed-file coverage. | QA + Flutter Lead |
| Corrupt local state fails closed but has no operator-facing export/repair path. | A user may be blocked from launching after unrecoverable corruption. | Design non-destructive diagnostics and recovery UX; never auto-delete the store. | Persistence Owner |
| Whole-envelope persistence and unbounded History remain linear. | Large future History/Shopping datasets may increase latency and memory. | Establish volume budgets and profile before introducing paging or per-record storage. | Flutter Lead |
| Recommendation providers still observe broad Pantry projections. | Shopping-driven Pantry changes may trigger avoidable recomputation. | Profile rebuilds and add `select` or stable derived projections only where measured. | Flutter Lead |
| Canonical unknowns are deterministic but require ongoing catalog curation. | New regional or custom ingredients may remain unclassified. | Preserve deterministic IDs, surface diagnostics, and review catalog additions as versioned data changes. | Domain Data Owner |
| Transaction coordinator and Hive repository are large implementation units. | Review and maintenance cost may grow as purchase flows are added. | Extend through existing contracts; extract only proven seams with characterization tests. | Principal Engineer |
| Runtime font fetching may reduce first-run offline determinism. | Visual fallback or network attempt while offline. | Bundle fonts or explicitly disable runtime fetching. | Design System Owner |

## Deferred work

The following work is intentionally outside this readiness integration:

- Shopping List implementation
- Package sizing implementation
- Retailer identity implementation
- Price model implementation
- Shopping aggregation implementation
- Purchase workflow implementation
- Shopping UI implementation
- Cloud synchronization
- Broad page or folder refactors
- Storage-engine replacement

## Recommended Shopping Foundation sprint plan

1. **Decision gate:** accept the Shopping item/list lifecycle, package, retailer, price, aggregation, and purchase contracts.
2. **Domain first:** add Shopping entities and pure aggregation/package-rounding services using canonical ingredient and unit IDs.
3. **Persistence boundary:** add Shopping repository contracts and a versioned local schema; keep presentation storage-free.
4. **Transaction integration:** route purchase-to-Pantry mutations through `InventoryTransactionCoordinator` or an RFC-approved extension preserving journal and idempotency invariants.
5. **Migration and tests:** add fixtures for existing Pantry/History plus new Shopping schema; keep full coverage at or above 80%.
6. **UI last:** implement Shopping presentation only after domain, migration, and transaction tests pass.
7. **Release gate:** format, analyze, full tests with coverage, restart recovery, and offline validation before merge.

## CTO recommendation

Approve Shopping Foundation on source baseline `6bba09275bedf76944779f59a28042ce11727d11` with the following guardrails:

- no free-text ingredient identity where a canonical ID exists;
- no uncontracted quantity or unit conversion;
- no direct persistence from presentation;
- no Pantry mutation outside the durable transaction boundary;
- no Riverpod inventory publication before durable commit;
- no merge while format, analyzer, tests, migrations, or the 80% coverage floor is red;
- cloud synchronization remains out of scope.

Final verdict: **READY WITH MINOR DEFERRED WORK**

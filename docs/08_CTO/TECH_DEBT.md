# KinRaiDee Technical Debt Register

- Audit date: 2026-07-26
- Application commit: `d8631869bb7e0eb18a9cc136189006212f8737dd`
- Estimation unit: focused engineer-days, including tests and review

## P0 Critical

### TD-P0-01 - Pantry and Cooking History commits are not atomic

- **Evidence:** `PantryNotifier.applyQuantityTransaction` persists Pantry before `CookingHistoryNotifier.record` at `apps/mobile/lib/core/providers/pantry_provider.dart:248-253`. `CookingHistoryPage._applyAdjustment` persists Pantry before replacing History at `apps/mobile/lib/features/pantry/presentation/pages/cooking_history_page.dart:89-99`. Quick undo repeats the ordering at `pantry_provider.dart:292-298`.
- **Impact:** A Hive error, process termination, or restart between writes leaves inventory and audit history contradictory. Retrying can deduct twice or create misleading completion/cancellation state.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/features/pantry/presentation/providers/cooking_history_provider.dart`; `apps/mobile/lib/features/pantry/presentation/pages/cooking_history_page.dart`; `apps/mobile/lib/core/services/storage_service.dart`.
- **Recommended action:** Create one narrow application transaction boundary for completion, adjustment, cancellation, and undo. Define atomic storage or a persisted journal with idempotent commit/compensation and recovery. Add failure injection and restart tests before changing callers.
- **Estimated effort:** 5-8 engineer-days.
- **Blocks Shopping Foundation:** **Yes.** Shopping-to-Pantry transfer would add another unsafe cross-aggregate write.

### TD-P0-02 - Transaction preconditions and multi-item undo integrity are incomplete

- **Evidence:** `PantryNotifier.applyQuantityTransaction` assigns `afterQuantity` to matching IDs without checking current `beforeQuantity`, unit, missing IDs, or duplicate changes (`pantry_provider.dart:228-246`). `undoQuantityTransaction` persists any individually restorable lot but cancels History only if all restore (`260-301`). Tests at `apps/mobile/test/core/providers/pantry_provider_test.dart:85-203` use single-change transactions and do not cover partial multi-item restore.
- **Impact:** A stale plan can overwrite a later Pantry edit. A quick undo can partially restore inventory while leaving its History entry completed.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/features/pantry/domain/models/pantry_quantity_transaction.dart`; `apps/mobile/test/core/providers/pantry_provider_test.dart`; `apps/mobile/test/core/providers/pantry_completion_navigation_test.dart`.
- **Recommended action:** Validate all changes as one set before mutation; reject on any missing ID, changed quantity/unit, duplicate ID, or invalid transition. Define all-or-nothing undo or an explicit partial outcome reflected in History, then add multi-item and stale-transaction tests.
- **Estimated effort:** 3-5 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

## P1 High

### TD-P1-01 - Pantry and Recipe do not share canonical ingredient identity

- **Evidence:** Pantry `Ingredient` contains a lot ID and free-text name but no taxonomy ID (`apps/mobile/lib/core/models/ingredient.dart:1-42`). `FoodItem` has name/emoji/aliases only (`apps/mobile/lib/features/pantry/domain/models/food_category.dart:13-38`). Recipe requirements carry IDs (`recipe_ingredient.dart:1-28`), while `IngredientNameMatcher` normalizes names and applies hard-coded family aliases (`ingredient_name_matcher.dart:3-56`).
- **Impact:** Duplicate detection, shortage calculation, and merging across Pantry, Recipe, and Shopping are heuristic and can silently combine or split the wrong ingredient.
- **Affected files:** `apps/mobile/lib/core/models/ingredient.dart`; `apps/mobile/lib/features/pantry/domain/models/food_category.dart`; `apps/mobile/lib/features/recipe/domain/entities/ingredient.dart`; `apps/mobile/lib/features/recipe/domain/entities/recipe_ingredient.dart`; `apps/mobile/lib/features/recipe/domain/services/ingredient_name_matcher.dart`; `apps/mobile/assets/ingredients/thai_ingredients.json`.
- **Recommended action:** Approve canonical ingredient, Pantry lot, and variant/parent identities; map the Pantry catalogue to the recipe taxonomy; document migration and unknown-item behavior.
- **Estimated effort:** 5-10 engineer-days plus product/data review.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-02 - Unit semantics are free-text and only partially convertible

- **Evidence:** Pantry and transaction models store `unit` as `String` (`ingredient.dart:5-8`; `pantry_quantity_transaction.dart:5-12`). Recipe requirements also store a string unit (`recipe_ingredient.dart:5-10`). `RecipeServingCalculator` supports a bounded set of conversions in `apps/mobile/lib/features/recipe/domain/services/recipe_serving_calculator.dart`, with no canonical dimension or persisted unit ID.
- **Impact:** Shopping aggregation may merge incompatible dimensions or fail to merge equivalent spellings. Rounding behavior can diverge between display, deduction, and list generation.
- **Affected files:** `apps/mobile/lib/core/models/ingredient.dart`; `apps/mobile/lib/features/pantry/domain/models/pantry_quantity_transaction.dart`; `apps/mobile/lib/features/recipe/domain/entities/recipe_ingredient.dart`; `apps/mobile/lib/features/recipe/domain/services/recipe_serving_calculator.dart`; related serving calculator tests.
- **Recommended action:** Approve a quantity/unit value contract, canonical dimensions, aliases, conversion precision, and rejection policy before Shopping quantities are persisted.
- **Estimated effort:** 4-7 engineer-days plus data cleanup.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-03 - Repository boundaries are bypassed by presentation providers

- **Evidence:** `CookingHistoryNotifier` loads and saves through static `StorageService` calls (`apps/mobile/lib/features/pantry/presentation/providers/cooking_history_provider.dart:3-12,76-81`). `HeroSelectionNotifier` does the same for pinned recipes (`apps/mobile/lib/features/recipe/presentation/providers/recipe_provider.dart:96-145`).
- **Impact:** Persistence behavior is difficult to replace or failure-test, and Shopping has no consistent example to follow. Presentation becomes responsible for serialization and storage lifecycle.
- **Affected files:** `apps/mobile/lib/features/pantry/presentation/providers/cooking_history_provider.dart`; `apps/mobile/lib/features/recipe/presentation/providers/recipe_provider.dart`; `apps/mobile/lib/core/services/storage_service.dart`.
- **Recommended action:** Add domain-owned repository contracts and local implementations for History and persisted recipe preferences. Inject them through providers; forbid direct Hive/`StorageService` access from presentation.
- **Estimated effort:** 3-5 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-04 - Riverpod state is optimistic but persistence failures are not modeled

- **Evidence:** `PantryNotifier.addIngredient`, `updateIngredient`, `toggleFavorite`, and `applyQuantityTransaction` change state before awaiting repository writes (`apps/mobile/lib/core/providers/pantry_provider.dart:124-132,135-161,164-194,219-253`). `CookingHistoryNotifier.record` does the same (`cooking_history_provider.dart:17-25`). `PantryPage._addIngredient` has no storage-error handling (`pantry_page.dart:45-58`).
- **Impact:** UI can show data that was never durable. The next launch may appear to lose user changes, and callers cannot distinguish validation, loading, and storage failures.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/features/pantry/presentation/providers/cooking_history_provider.dart`; `apps/mobile/lib/features/pantry/presentation/pages/pantry_page.dart`; `apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart`.
- **Recommended action:** Define a consistent mutation result/error model, rollback or commit state only after durability, and add repository-failure tests. Adopt `AsyncNotifier` only where it clarifies observable loading/mutation state; do not rewrite all providers.
- **Estimated effort:** 4-6 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-05 - Mutable storage has no explicit schema/migration contract

- **Evidence:** `StorageService` stores Pantry, favourites, pins, and History as raw values in one `Box<dynamic>` (`apps/mobile/lib/core/services/storage_service.dart:9-17`). Pantry and History are complete lists of maps (`31-37,139-146`). History parsing can omit malformed entries (`106-136`); Pantry parsing defaults missing creation time and skips invalid records (`171-199`).
- **Impact:** A model change can silently alter or drop local user data. Shopping would increase the number of unversioned shapes and complicate rollback.
- **Affected files:** `apps/mobile/lib/core/services/storage_service.dart`; `apps/mobile/lib/features/pantry/domain/models/cooking_history_entry.dart`; `apps/mobile/lib/core/models/ingredient.dart`.
- **Recommended action:** Define versioned payloads, isolated repository migration ownership, observable migration failures, backups/recovery, and fixture-based upgrade tests before adding a Shopping schema.
- **Estimated effort:** 5-8 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-06 - Completion feedback and quick undo have two presentation owners

- **Evidence:** `PantryNotifier.applyQuantityTransaction` publishes a completion event and navigates to Pantry (`apps/mobile/lib/core/providers/pantry_provider.dart:251-256`). `MainShell` clears and shows global feedback (`apps/mobile/lib/app/navigation/main_shell.dart:35-62`). `RecipeDetailPage._finishCooking` also clears and shows a local undo snackbar (`apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart:153-166`).
- **Impact:** Navigation can clear the local undo action or produce duplicate/transient feedback. The only direct recovery affordance can disappear while the non-atomic transaction remains committed.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/app/navigation/main_shell.dart`; `apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart`; `apps/mobile/lib/app/navigation/cooking_completion_provider.dart`.
- **Recommended action:** Choose one feedback owner, define undo lifetime across navigation, and add a widget/integration test for completion -> Pantry -> undo.
- **Estimated effort:** 2-3 engineer-days.
- **Blocks Shopping Foundation:** **Yes**, as a stabilization gate for transaction UX.

### TD-P1-07 - The full test gate is red and relies on the wall clock

- **Evidence:** Full `flutter test` produced 58 passes and one failure at `apps/mobile/test/features/recipe/pantry_deduction_planner_test.dart:60`. The test fixes `now` and expiries to 2026-07-24/25/30 (`11,29-38`), while `Ingredient.isExpired` and `daysUntilExpiry` call `DateTime.now` (`apps/mobile/lib/core/models/ingredient.dart:70-91`).
- **Impact:** Results change with calendar date, so CI cannot be trusted as a release gate. The same implicit clock affects matching, deduction, and recommendation behavior.
- **Affected files:** `apps/mobile/lib/core/models/ingredient.dart`; `apps/mobile/test/features/recipe/pantry_deduction_planner_test.dart`; services that read the entity expiry getters.
- **Recommended action:** Introduce an injectable clock or pass `now` through expiry-sensitive calculations, update fixed-time tests, and rerun the entire suite.
- **Estimated effort:** 1-2 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-08 - Critical workflow and failure-path coverage is near zero

- **Evidence:** Audit coverage is 1,557/3,797 lines (41%). `RecipeDetailPage` covers 2/510 lines, `CookingHistoryPage` 2/233, and `StorageService` 35/106. Existing unit tests strongly cover planners, but no test injects a failure between Pantry and History writes, restarts after a partial write, or exercises a partial multi-item undo.
- **Impact:** The highest-risk orchestration can regress despite high domain-service coverage. Refactoring for Shopping cannot be proven behavior-preserving.
- **Affected files:** `apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart`; `apps/mobile/lib/features/pantry/presentation/pages/cooking_history_page.dart`; `apps/mobile/lib/core/services/storage_service.dart`; `apps/mobile/test/core/providers/pantry_provider_test.dart`.
- **Recommended action:** Add application-service tests with controllable repositories, Hive migration/restart tests, and a small number of end-to-end widget tests for completion, cancel, adjust, and undo.
- **Estimated effort:** 5-8 engineer-days.
- **Blocks Shopping Foundation:** **Yes.**

### TD-P1-09 - The current application is not established on the default branch

- **Evidence:** Git history at audit shows `feature/cooking-history-4.8.1` at `d8631869` is 176 commits ahead of `main` at `b27e1762`, with no pull request. The audited branch contains active workflow files such as `apps/mobile/lib/features/pantry/presentation/pages/cooking_history_page.dart`, `apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart`, and their tests that are not represented by the default branch baseline.
- **Impact:** Starting Shopping from `main` omits the current product; starting from an unreviewed feature branch makes a new foundation depend on an unstable source of truth.
- **Affected files:** The full current application tree, especially the transaction and History files named above.
- **Recommended action:** Open, review, and reconcile the current application branch; define the approved Shopping base SHA before any feature branch is created.
- **Estimated effort:** 2-5 engineer-days, excluding fixes discovered in review.
- **Blocks Shopping Foundation:** **Yes.**

## P2 Medium

### TD-P2-01 - Core depends on app navigation and feature presentation

- **Evidence:** `apps/mobile/lib/core/providers/pantry_provider.dart:3-10` imports app navigation, Pantry data implementation, domain types, a presentation History provider, and storage.
- **Impact:** `core` cannot be reused as a stable lower layer, and changes in navigation or UI feedback can force domain mutation changes.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/app/navigation/cooking_completion_provider.dart`; `apps/mobile/lib/app/navigation/main_shell.dart`.
- **Recommended action:** During the transaction-safety work, move only the cooking mutation orchestration behind an application interface and keep navigation/feedback in presentation.
- **Estimated effort:** 3-5 engineer-days, overlapping TD-P0-01.
- **Blocks Shopping Foundation:** **Yes**, but should be fixed narrowly rather than through a broad folder rewrite.

### TD-P2-02 - Whole-list persistence and unbounded History will degrade

- **Evidence:** `StorageService.saveIngredients` and `saveCookingHistory` rewrite complete lists (`apps/mobile/lib/core/services/storage_service.dart:31-37,139-146`). History load returns the full sorted collection; `CookingHistoryPage` renders from that collection (`cooking_history_page.dart`).
- **Impact:** Write cost, serialization time, and memory use grow linearly. History has no retention, pagination, or indexed query boundary.
- **Affected files:** `apps/mobile/lib/core/services/storage_service.dart`; `apps/mobile/lib/features/pantry/presentation/providers/cooking_history_provider.dart`; `apps/mobile/lib/features/pantry/presentation/pages/cooking_history_page.dart`.
- **Recommended action:** Define expected scale and measure first. Add repository query/paging boundaries; move to per-record storage only when measurements or migration work justify it.
- **Estimated effort:** 4-8 engineer-days after instrumentation.
- **Blocks Shopping Foundation:** **No**, at current local data scale.

### TD-P2-03 - Broad provider watches cause avoidable recomputation

- **Evidence:** `HomePage` watches the entire Pantry list (`apps/mobile/lib/features/home/presentation/pages/home_page.dart:19`). Recipe match and smart recommendation providers watch the entire Pantry list (`apps/mobile/lib/features/recipe/presentation/providers/recipe_provider.dart:22-27,154-172`). `MainShell` keeps all tab pages mounted in an `IndexedStack` (`apps/mobile/lib/app/navigation/main_shell.dart:65-76`).
- **Impact:** Every quantity or favourite change can rebuild off-screen consumers and recompute matching/ranking across all 158 recipes.
- **Affected files:** `apps/mobile/lib/features/home/presentation/pages/home_page.dart`; `apps/mobile/lib/features/recipe/presentation/providers/recipe_provider.dart`; `apps/mobile/lib/app/navigation/main_shell.dart`; `apps/mobile/lib/features/pantry/presentation/pages/pantry_page.dart`.
- **Recommended action:** Profile rebuilds, add `select`/derived stable projections where measured, debounce fuzzy search, and cache matching inputs. Keep `IndexedStack` unless profiling shows it is material.
- **Estimated effort:** 3-6 engineer-days.
- **Blocks Shopping Foundation:** **No**, but Shopping providers should avoid copying the broad-watch pattern.

### TD-P2-04 - Large pages combine layout and workflows

- **Evidence:** `RecipeDetailPage` is 1,191 lines, `RecipePage` 663, `PantryPage` 584, and `CookingHistoryPage` 550. `RecipeDetailPage._finishCooking` (`74-166`) and `CookingHistoryPage._applyAdjustment` (`72-99`) own workflow sequencing.
- **Impact:** Review and testing are harder; UI changes can accidentally modify transaction behavior.
- **Affected files:** The four page files listed above.
- **Recommended action:** Extract workflow orchestration first as required for transaction safety. Decompose widgets only when a feature change touches a stable section.
- **Estimated effort:** 3-7 engineer-days incrementally.
- **Blocks Shopping Foundation:** **No** beyond the transaction workflow extraction.

### TD-P2-05 - Formatting baseline is not clean

- **Evidence:** `dart format --output=none --set-exit-if-changed lib test` reports 40 of 87 Dart files would change.
- **Impact:** Future product diffs will mix behavior with mechanical formatting and create avoidable merge/review noise.
- **Affected files:** 40 files reported by the formatter across `apps/mobile/lib` and `apps/mobile/test`.
- **Recommended action:** Make a dedicated no-behavior formatting commit on the approved source base, then enforce the check in CI.
- **Estimated effort:** 0.5-1 engineer-day plus review.
- **Blocks Shopping Foundation:** **Yes** as a clean-branch gate, not an architectural blocker.

### TD-P2-06 - App startup has no storage failure path

- **Evidence:** `apps/mobile/lib/main.dart:7-12` awaits `StorageService.init()` before `runApp` without fallback, recovery UI, logging, or corrupt-box handling.
- **Impact:** Hive initialization or migration failure can prevent the app from launching, leaving the user without a recovery path.
- **Affected files:** `apps/mobile/lib/main.dart`; `apps/mobile/lib/core/services/storage_service.dart`.
- **Recommended action:** Define startup failure telemetry and a non-destructive recovery screen; test corrupt/locked store scenarios before adding migrations.
- **Estimated effort:** 2-4 engineer-days.
- **Blocks Shopping Foundation:** **No**, but it should accompany the schema migration design.

### TD-P2-07 - Typography may fetch at runtime

- **Evidence:** `apps/mobile/lib/app/theme/app_theme.dart:1-25` uses `google_fonts` without bundling the selected fonts or setting `GoogleFonts.config.allowRuntimeFetching = false`.
- **Impact:** Core data works offline, but first-run typography is not fully deterministic and can cause network attempts or fallback rendering.
- **Affected files:** `apps/mobile/lib/app/theme/app_theme.dart`; `apps/mobile/pubspec.yaml`.
- **Recommended action:** Bundle required font assets or explicitly disable runtime fetching and verify offline rendering.
- **Estimated effort:** 0.5-1 engineer-day.
- **Blocks Shopping Foundation:** **No.**

## P3 Low

### TD-P3-01 - Domain models use ambiguous duplicate names

- **Evidence:** Pantry stock is `core/models/Ingredient` while the Recipe taxonomy also declares `features/recipe/domain/models/Ingredient`; recipe requirements use `RecipeIngredient`.
- **Impact:** Imports and reviews can confuse stock lots, canonical ingredients, and recipe requirements.
- **Affected files:** `apps/mobile/lib/core/models/ingredient.dart`; `apps/mobile/lib/features/recipe/domain/entities/ingredient.dart`; `apps/mobile/lib/features/recipe/domain/entities/recipe_ingredient.dart`.
- **Recommended action:** After identity ADRs and migrations are approved, adopt explicit names such as Pantry lot, canonical ingredient, and recipe requirement. Avoid a rename-only refactor before the model decision.
- **Estimated effort:** 1-3 engineer-days after model work.
- **Blocks Shopping Foundation:** **No.**

### TD-P3-02 - Navigation ownership is mixed into mutation state

- **Evidence:** `PantryNotifier.applyQuantityTransaction` calls `pantryTabNavigationProvider` at `apps/mobile/lib/core/providers/pantry_provider.dart:254-256`, while pages also use route navigation around the same workflow.
- **Impact:** Domain mutation tests require navigation providers, and mutation reuse from future Shopping flows can cause unexpected tab changes.
- **Affected files:** `apps/mobile/lib/core/providers/pantry_provider.dart`; `apps/mobile/lib/app/navigation/main_shell.dart`; `apps/mobile/lib/features/recipe/presentation/pages/recipe_detail_page.dart`.
- **Recommended action:** Return a typed completion result and let the initiating presentation flow decide navigation.
- **Estimated effort:** 1-2 engineer-days, preferably within TD-P0-01.
- **Blocks Shopping Foundation:** **No**, if Shopping does not reuse the current notifier directly.

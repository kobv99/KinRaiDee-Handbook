# KinRaiDee Architecture Readiness Review

- Audit date: 2026-07-26
- Application repository: `kobv99/KinRaiDee`
- Audited application commit: `d8631869bb7e0eb18a9cc136189006212f8737dd`
- Audited branch: `feature/cooking-history-4.8.1`
- Application default branch at audit: `main` at `b27e1762c5eada0a9753796c7d11d0468534d801`
- Handbook base commit: `9647c2f0540db22f2802fd3bf591983a90dead78`
- Shopping readiness verdict: **NOT READY**

## Executive summary

KinRaiDee has a credible offline Flutter foundation and unusually strong unit coverage around recipe matching, serving calculations, recommendation ranking, pantry deduction planning, and history delta calculations. The application contains 70 production Dart files, 17 test files, 158 bundled recipes, and a 57-item canonical recipe ingredient asset. Static analysis passes with no findings.

The architecture is not ready for Shopping Foundation because the current cooking transaction is not atomic, repository boundaries are only partially implemented, and Pantry has no stable canonical ingredient identity that Shopping can use for duplicate detection and recipe-shortage generation. `PantryNotifier.applyQuantityTransaction` persists Pantry before `CookingHistoryNotifier.record`; `CookingHistoryPage._applyAdjustment` similarly persists Pantry before replacing History. Neither path compensates when the second write fails. This directly conflicts with the transaction rules in `docs/03_Architecture/TRANSACTION_ENGINE.md`.

The source-of-truth state also requires stabilisation. The audited branch is 176 commits ahead of `main`, includes the complete current application architecture, and has no pull request. Starting Shopping from `main` would omit Pantry dashboard, recipe, recommendation, deduction, and history work; starting from the feature branch without review would make Shopping depend on an unreviewed base.

The current full test run reports 58 passing tests and one failing test. The failure is time-dependent: `test/features/recipe/pantry_deduction_planner_test.dart:11-38` fixes expiry dates at 2026-07-25 and 2026-07-30, while `Ingredient.isExpired` at `apps/mobile/lib/core/models/ingredient.dart:70-79` reads the wall clock. Formatting verification also reports 40 unformatted files. Measured line coverage is 41%, with strong domain-service coverage but almost no coverage of `RecipeDetailPage` (0.4%) or `CookingHistoryPage` (0.9%), where multi-repository workflows are currently orchestrated.

No production code was changed during this audit.

## Scores

| Area | Score | Evidence-based rationale |
|---|---:|---|
| Architecture | 5.0 / 10 | Feature folders and pure domain services exist, but there is no application/use-case layer; `core/providers/pantry_provider.dart:3-10` depends on app navigation, feature data, feature domain, and feature presentation. |
| Maintainability | 5.0 / 10 | Domain calculations are generally small and testable, but `RecipeDetailPage` is 1,191 lines, `RecipePage` 663, `PantryPage` 584, and `CookingHistoryPage` 550. |
| Scalability | 4.5 / 10 | Every Pantry and History mutation rewrites a complete list through `StorageService.saveIngredients` and `saveCookingHistory` at `storage_service.dart:31-37` and `139-146`; recommendation matching is recomputed from the full Pantry list. |
| Offline-first compliance | 8.0 / 10 | Pantry and History use local Hive and recipes use bundled assets. `AppTheme` uses runtime `GoogleFonts` at `app_theme.dart:1-25` without disabling runtime fetching, so typography is not fully deterministic offline. |
| Repository compliance | 4.0 / 10 | Pantry has a repository interface, but `CookingHistoryNotifier` and `HeroSelectionNotifier` call `StorageService` directly from presentation providers. |
| Riverpod compliance | 5.5 / 10 | Provider overrides make Pantry tests practical, but notifiers expose synchronous state for fallible persistence, perform writes after optimistic state changes, and use broad whole-list watches. |
| Testability | 6.0 / 10 | Domain services have 84–100% line coverage in critical areas, but overall coverage is 41%, one test is time-dependent, and transaction failure/restart paths are untested. |

## Current architecture

### Physical structure

```text
apps/mobile/lib
├── app
│   ├── navigation
│   ├── router
│   └── theme
├── core
│   ├── models
│   ├── providers
│   └── services
├── features
│   ├── home/presentation
│   ├── pantry/{data,domain,presentation}
│   ├── profile/presentation
│   ├── recipe/{data,domain,presentation}
│   └── shopping/presentation
└── shared/widgets
```

The intended presentation/application/domain/infrastructure layers are only partly represented. Pantry and Recipe each have repository contracts, but there is no application layer. Orchestration is split among UI pages and Riverpod notifiers:

- `RecipeDetailPage._finishCooking` builds a deduction plan and transaction, then invokes `PantryNotifier.applyQuantityTransaction` (`recipe_detail_page.dart:74-166`).
- `PantryNotifier.applyQuantityTransaction` updates Pantry, records History, publishes a UI event, and navigates (`pantry_provider.dart:219-257`).
- `CookingHistoryPage._applyAdjustment` plans a delta, applies a Pantry transaction, and replaces the History entry (`cooking_history_page.dart:72-99`).

Persistence uses one dynamic Hive box, `pantry_box`, with four logical keys defined by `StorageService` (`storage_service.dart:9-17`). Recipes and the recipe ingredient master are bundled JSON assets loaded by `LocalRecipeDataSource` and `IngredientCatalog`.

### Feature boundaries

- Pantry owns inventory lots, filters, search, expiry priority, quantity transaction types, and cooking-history models.
- Recipe owns recipe assets, matching, serving calculations, deduction planning, and recommendation ranking.
- Recipe domain services import the Pantry/core inventory model, and Pantry presentation imports Recipe domain services/providers (`pantry_page.dart:11-12`; `pantry_use_soon_section.dart:7-8`).
- Shopping currently contains only `ShoppingPage`, a presentation placeholder at `apps/mobile/lib/features/shopping/presentation/pages/shopping_page.dart:1-14`.

## Strengths

1. **Offline data paths are real, not aspirational.** `StorageService.init` opens local Hive before `runApp` (`main.dart:7-12`), and `LocalRecipeDataSource.defaultAssetPaths` loads nine bundled recipe packs (`local_recipe_datasource.dart:8-45`).
2. **Domain calculations are mostly Flutter-independent.** `RecipeMatcher`, `RecipeServingCalculator`, `PantryDeductionPlanner`, `SmartRecommendationEngine`, `PantryExpiryPriority`, and `CookingHistoryAdjustmentPlanner` are plain Dart services with direct unit tests.
3. **History correction uses deltas.** `CookingHistoryAdjustmentPlanner.adjust` calculates `consumptionDelta` against current Pantry state and generates before/after quantity changes (`cooking_history_adjustment_planner.dart:58-97`).
4. **History snapshots preserve original and adjusted usage.** `CookingHistoryChange` stores `beforeQuantity`, `originalAfterQuantity`, and `afterQuantity` (`cooking_history_entry.dart:5-30`), and the JSON round-trip is covered by `cooking_history_entry_test.dart:6-49`.
5. **The recipe repository boundary is clean for bundled data.** `RecipeRepository` is domain-owned and `LocalRecipeRepository` delegates to a data source (`recipe_repository.dart:1-5`; `local_recipe_repository.dart:1-14`).
6. **Riverpod dependencies are overrideable in Pantry tests.** `pantryRepositoryProvider` is overridden by fakes in `pantry_provider_test.dart:23-28` and `pantry_completion_navigation_test.dart:15-34`.
7. **Important matching regressions are explicitly tested.** `ingredient_name_matching_regression_test.dart` covers egg/chicken collisions and meat-family aliases.

## Weaknesses

1. **There is no application transaction boundary.** UI and providers coordinate multi-store operations directly; no use case owns commit, rollback, idempotency, or recovery.
2. **The Pantry model is a lot record without canonical ingredient identity.** `core/models/ingredient.dart:1-42` has only a generated lot `id` and free-text `name`; `AddIngredientDialog._submit` generates the ID from the current microsecond (`add_ingredient_dialog.dart:97-108`).
3. **Persistence is a global static service and whole-list store.** `StorageService` mixes Hive lifecycle, Pantry serialization, History serialization, favourites, and hero-selection preferences in one class (`storage_service.dart:6-224`).
4. **Providers cannot represent persistence loading or failure.** `PantryNotifier` and `CookingHistoryNotifier` expose synchronous collections and update state before awaiting writes.
5. **Large presentation files own workflow logic.** The four largest pages total 2,988 lines and include validation, calculation invocation, mutation sequencing, and feedback.
6. **The app has two ingredient catalogues.** `food_category.dart` contains 104 hard-coded `FoodItem` entries without identifiers; `assets/ingredients/thai_ingredients.json` contains 57 canonical entries with IDs and parent relationships.
7. **Time is not consistently injectable.** `PantryExpiryPriority` accepts `now`, but `Ingredient.isExpired`, `Ingredient.daysUntilExpiry`, Recipe matching, serving, deduction, and recommendation call the wall clock through the entity getters.

## Architecture violations

### AR-01 — Pantry and History are not one atomic business transaction

`PantryNotifier.applyQuantityTransaction` sets state and saves Pantry at `pantry_provider.dart:248-249`, then records History at `251-253`. There is no rollback if History persistence fails. History adjustment repeats the two-step write in `CookingHistoryPage._applyAdjustment` at `cooking_history_page.dart:89-99`. Quick undo saves Pantry at `pantry_provider.dart:292-293` before marking History cancelled at `294-298`.

This violates the required atomic outcome and recovery rules. It blocks Shopping because a future shopping-to-pantry operation would inherit the same unsafe orchestration pattern.

### AR-02 — Transaction application does not validate preconditions

`PantryNotifier.applyQuantityTransaction` indexes changes by ID and assigns each `afterQuantity` (`pantry_provider.dart:228-246`) without verifying:

- every transaction ingredient still exists;
- current quantity equals `beforeQuantity`;
- current unit equals the transaction unit;
- transaction IDs are unique;
- the requested transition is non-negative and directionally valid.

Missing Pantry items are ignored while all transaction changes are still recorded in History. Stale transactions can overwrite later edits. `pantry_provider_test.dart` covers normal apply/undo and a single-item edited-after-deduction case, but not missing IDs, stale apply, duplicate IDs, changed units, or partial multi-item apply.

### AR-03 — Quick undo can create a partial restoration with unchanged History

`undoQuantityTransaction` restores each matching item independently (`pantry_provider.dart:272-286`) and persists if at least one item was restored (`288-293`). It marks History cancelled only when every item restored (`294-298`). A multi-item transaction can therefore partially restore Pantry while History remains completed. Existing tests at `pantry_provider_test.dart:85-203` cover only one changed ingredient per transaction.

### AR-04 — Presentation providers access persistence directly

- `CookingHistoryNotifier.build` and `_persist` call `StorageService` from `features/pantry/presentation/providers/cooking_history_provider.dart:3-12,76-81`.
- `HeroSelectionNotifier` loads and saves pinned state through `StorageService` from `features/recipe/presentation/providers/recipe_provider.dart:4,96-145`.

This violates the Handbook rule that persistence is accessed through repository boundaries and leaves no approved repository pattern for Shopping lifecycle state.

### AR-05 — Dependency direction is inverted through `core`

`core/providers/pantry_provider.dart:3-10` imports:

- app navigation;
- a UI event provider;
- Pantry data implementation;
- Pantry domain models and repository;
- a Pantry presentation provider;
- global storage.

`PantryNotifier` consequently owns domain mutation, persistence coordination, History, feedback events, and navigation. `core` is not a stable lower layer; it depends upward on app and presentation.

### AR-06 — Completion feedback has two owners

After `PantryNotifier.applyQuantityTransaction` publishes a completion and opens Pantry (`pantry_provider.dart:251-256`), `MainShell` clears snackbars and shows global completion feedback (`main_shell.dart:35-62`). `RecipeDetailPage._finishCooking` also clears snackbars and shows an eight-second local snackbar with quick undo (`recipe_detail_page.dart:153-166`). Navigation can remove the recipe route and clear its snackbar, making the undo action transient or causing duplicate feedback. There is no widget test covering the complete flow.

### AR-07 — Pantry, Recipe, and Shopping lack a shared ingredient identity contract

The Pantry `Ingredient` model has no canonical ID (`core/models/ingredient.dart:1-42`). Pantry catalogue `FoodItem` has only name, emoji, and aliases (`food_category.dart:13-38`). Recipe requirements use canonical-looking IDs (`recipe_ingredient.dart:1-28`), while matching falls back to normalized names and a hard-coded family alias map (`ingredient_name_matcher.dart:3-56`). Shopping cannot safely merge “หมู”, “เนื้อหมู”, “สันคอหมู”, or multiple Pantry lots without an approved identity rule.

### AR-08 — Persistence has no explicit schema or migration boundary

All mutable data is stored as raw lists/maps in one `Box<dynamic>` (`storage_service.dart:9-17,31-37,139-146`). Pantry records have no schema version; History parsing silently defaults or drops invalid records (`storage_service.dart:106-136,171-199`; `cooking_history_entry.dart:162-190`). No migration test or corrupt-data reporting exists.

### AR-09 — Fallible writes update Riverpod state before durability

Examples include `PantryNotifier.addIngredient` (`pantry_provider.dart:124-132`), `updateIngredient` (`135-161`), `toggleFavorite` (`164-194`), `applyQuantityTransaction` (`219-253`), and `CookingHistoryNotifier.record` (`cooking_history_provider.dart:17-25`). If Hive throws, UI state can disagree with persisted state. UI callers such as `PantryPage._addIngredient` at `pantry_page.dart:45-58` do not catch or present write failures.

### AR-10 — Test and formatting gates are not green

- Full `flutter test`: 58 pass, 1 fail.
- The failing expectation is `pantry_deduction_planner_test.dart:60`; its fixed dates are at lines 11 and 29-38, while production expiry reads `DateTime.now` at `ingredient.dart:70-91`.
- `dart format --output=none --set-exit-if-changed lib test`: 40 files would change.
- Overall measured line coverage: 1,557 / 3,797 lines, 41%.
- `RecipeDetailPage`: 2 / 510 covered lines; `CookingHistoryPage`: 2 / 233; `StorageService`: 35 / 106.

## Risk summary

The highest risks are Pantry/History divergence after partial failure, stale transactions overwriting later state, partial quick undo, incompatible ingredient identities entering Shopping, and adding another direct-Hive presentation provider. Detailed probability, triggers, mitigations, and owners are recorded in `docs/08_CTO/RISK_REGISTER.md`.

## Validation results

The commands were executed from `apps/mobile` on an isolated archive of application commit `d8631869`.

| Command | Result |
|---|---|
| `flutter pub get` | Dependencies resolved and downloaded; command exited non-zero because Windows Developer Mode/symlink support is disabled. |
| `flutter analyze --no-pub` | **PASS** — no issues found in 6.4 seconds. |
| `flutter test --no-pub --reporter expanded` | **FAIL** — 58 passed, 1 failed. |
| `dart format --output=none --set-exit-if-changed lib test` | **FAIL** — 40 of 87 files require formatting. |
| Pantry/History/Cancel/Undo targeted group | **PASS** — 12 tests. |
| Recipe/Deduction/Serving targeted group | **FAIL** — 11 passed, 1 time-dependent expiry test failed. |
| Recommendation targeted group | **PASS** — 22 tests. |
| `flutter test --no-pub --coverage` | Same single failure; line coverage 41%. |

No additional project-specific lint, CI, or formatting scripts exist beyond `analysis_options.yaml`, `flutter analyze`, `flutter test`, and Dart formatting.

## Readiness for Shopping Foundation

**Verdict: NOT READY**

Shopping implementation must not begin until all required gates below are satisfied:

1. Reconcile `feature/cooking-history-4.8.1` into a reviewed source branch/PR and establish the approved base commit.
2. Make Pantry + History completion, history adjustment, cancellation, and quick undo atomic or compensating through one application service.
3. Validate transaction preconditions and prevent partial multi-item undo/history divergence.
4. Resolve Shopping specification decisions and accept the repository, transaction, and Shopping-domain ADRs currently listed as proposed in `DECISIONS.md`.
5. Define canonical ingredient identity and unit-normalisation contracts shared by Pantry, Recipe, and Shopping.
6. Add repository boundaries for History and persisted recommendation preferences; Shopping must not call Hive or `StorageService` from presentation.
7. Remove the duplicate completion snackbar owner and test the end-to-end completion/undo route.
8. Make analysis, formatting, full tests, and relevant failure-path tests green.

## Recommended next action

Run a focused architecture-readiness sprint before Shopping:

1. **Source stabilisation:** open and review the Cooking History source PR, fix the time-dependent test, format the branch, and confirm the intended base.
2. **Transaction safety:** introduce a narrow cooking/history application service and repository contracts with failure-injection tests. Do not change user-visible behaviour.
3. **Identity decision:** approve a canonical ingredient/lot/unit model and migration approach in ADR-003, ADR-004, and ADR-005 or their replacements.
4. **Completion cleanup:** select one completion-feedback owner and retain exactly one tested undo/cancel workflow.
5. **Shopping readiness review:** rerun this audit's gates and change the verdict only when the full suite is green and the architecture contracts are accepted.

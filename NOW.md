# Now

_Last updated: 2026-07-26_

This document answers one question: **What should the engineering team work on now?**

## Current priority

**Hold the stable architecture baseline and await CTO approval for Shopping Foundation.**

Shopping readiness verdict: **READY WITH MINOR DEFERRED WORK**

The architecture audit, RFC-0003, Transaction Engine, Canonical Ingredient System, and persistence-boundary stabilization are merged. Do not start Shopping implementation until the CTO explicitly approves it.

## Verified gate

- Source `main`: `6bba09275bedf76944779f59a28042ce11727d11`
- Format: pass, 115 files and 0 changes
- Analyzer: pass, 0 issues
- Tests: pass, 108 tests
- Line coverage: 80.77% (`4,188 / 5,185`)
- Migration and recovery targets: pass
- Direct persistence access from presentation: none found

## Next sprint recommendation after approval

1. Close the domain decisions for Shopping item identity, package sizing, retailer identity, price representation, aggregation, and purchase lifecycle.
2. Implement Shopping domain models and repository contracts against canonical ingredient IDs and the unit contract.
3. Add aggregation, package rounding, unknown-item, migration, and transaction integration tests before UI work.
4. Implement the purchase-to-Pantry workflow through `InventoryTransactionCoordinator`.
5. Add Shopping UI only after the domain and durability gates are green.
6. Keep cloud synchronization out of the first Shopping Foundation sprint.

## Minor deferred engineering work

- Add CI enforcement for formatting, analyzer, tests, and the 80% coverage floor.
- Define local corruption repair/export UX while retaining fail-closed startup behavior.
- Establish volume and rebuild budgets for History, recommendations, and future Shopping lists.
- Continue canonical catalog curation and unknown-ingredient diagnostics.
- Address large-file decomposition incrementally when touched by approved work.
- Make offline typography deterministic.

These items do not block the start of Shopping Foundation, but must not regress during it.

## Do not start

- Shopping implementation before CTO approval
- Cloud synchronization
- Nutrition
- Meal planning
- Authentication
- Family sharing
- Vision AI
- Direct generative-AI database access

## Stop conditions

Stop and request direction when:

- the handbook conflicts with source `main`;
- a Shopping model bypasses canonical ingredient IDs or unit contracts;
- purchase completion bypasses `InventoryTransactionCoordinator`;
- presentation imports Hive or `StorageService`;
- Riverpod publishes inventory state before durable commit;
- migration cannot preserve existing Pantry or Cooking History;
- format, analyzer, tests, or the 80% coverage gate becomes red;
- work expands beyond the approved Shopping Foundation scope.

# Feature Inventory

This document is the authoritative capability inventory. Status must be verified against the application repository before implementation begins.

| Capability | Status | Notes |
|---|---|---|
| Pantry management | Implemented | Core offline inventory capability |
| Recipe management | Implemented | Recipe data and cooking flow |
| Smart recommendations | Implemented | Uses available pantry context |
| Recipe serving adjustment | Implemented | Scales required ingredient amounts |
| Use-soon recommendations | Implemented | Prioritises ingredients nearing expiry |
| Recipe coverage | Implemented | Shows pantry coverage for recipes |
| Pantry deduction | Implemented | Deducts ingredients after cooking |
| Cooking history | Implemented | Records completed cooking activity |
| Cooking cancellation | Implemented, verify | Restores pantry amounts; latest known fix retained adjusted returned amount |
| Shopping Foundation | Next | Current major milestone |
| Nutrition | Planned | Begins after Shopping stabilisation |
| Meal planner | Planned | Depends on recipe, pantry, shopping, and nutrition maturity |
| AI Chef | Future | Tool-based AI, not direct database access |
| Vision capture | Future | Optional ingredient and receipt recognition |
| Cloud sync | Future | Optional; must preserve offline-first operation |
| Family sharing | Future | Depends on identity and sync architecture |

## Status definitions

- **Implemented** — present in source and expected to work; still subject to test verification.
- **Implemented, verify** — present but requires focused validation before depending on it.
- **Next** — approved direction for the next major milestone.
- **Planned** — sequenced after the current milestone.
- **Future** — long-term direction; do not start without an approved RFC or feature specification.

## Update rule

Every source PR that adds, removes, or materially changes a capability must update this file and the relevant feature specification.

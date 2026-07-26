# Architecture Overview

## Architectural objective

KinRaiDee must remain trustworthy as it grows from a Flutter application into a broader Kitchen Operating System. The architecture therefore prioritises deterministic domain rules, offline capability, testability, and clear boundaries between product data, UI, storage, and future AI services.

## Current logical flow

```text
Pantry data
  -> Recommendation logic
  -> Recipe selection and scaling
  -> Cooking completion
  -> Quantity transaction
  -> Persistent cooking history
```

## Layer responsibilities

### Presentation

Flutter screens and widgets render state, collect user intent, and display outcomes. They must not own reusable business calculations.

### Application state

Providers coordinate loading, state transitions, and calls into domain or storage services. Providers should remain understandable and should not become the permanent home of complex algorithms.

### Domain

Models, planners, validators, and calculation rules represent product behaviour. Domain code should be testable independently of Flutter rendering.

### Persistence

Storage services load and save local state. Persistence details must not leak into UI or future AI tools.

## Inventory transaction rule

Inventory changes are transactions, not arbitrary assignments. Cooking, history correction, cancellation, purchasing, and future waste recording must express quantity changes explicitly and validate them before persistence.

For historical correction, use deltas:

```text
new pantry adjustment = old recorded usage - corrected usage
```

Do not overwrite present pantry stock with reconstructed historical stock.

## Offline-first architecture

Core capabilities must work locally:

- pantry CRUD;
- recommendation using local data;
- recipe browsing from installed data;
- cooking completion;
- pantry deduction;
- history review and correction;
- shopping list management.

Cloud sync, shared households, remote content, and hosted AI may be added later, but they must not become hidden requirements for core workflows.

## Future modular direction

Potential domain modules:

```text
pantry_engine
recipe_engine
recommendation_engine
shopping_engine
nutrition_engine
ai_engine
```

These names describe desired boundaries, not an instruction to create packages immediately. A package extraction is justified when:

- the domain boundary is stable;
- dependencies can be made explicit;
- tests become easier;
- coupling is reduced;
- the move does more than rearrange files.

## AI boundary

Future AI must operate through explicit domain tools, such as Inventory, Recipe, History, Shopping, Nutrition, Expiration, and Budget tools.

AI must not:

- read local storage directly;
- silently alter pantry quantities;
- bypass validation;
- become the only path to core product value.

## Architecture review triggers

An ADR is normally required when a change introduces:

- a new persistence technology;
- cloud synchronisation;
- authentication or household sharing;
- a new package boundary;
- an AI runtime or model distribution strategy;
- a breaking data migration;
- background processing with platform-specific behaviour;
- a new source of truth for pantry, recipe, or nutrition data.

---

Status: Active  
Owner: CTO Office  
Last updated: 2026-07-25

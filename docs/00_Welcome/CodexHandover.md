# Codex Project Handover

## Purpose

This document transfers the KinRaiDee product and engineering context to Codex, acting as Principal Software Engineer. Read this file before modifying the application repository.

## Product identity

**Product:** KinRaiDee — AI Cooking Platform  
**Product repository:** `kobv99/KinRaiDee`  
**Handbook repository:** `kobv99/KinRaiDee-Handbook`

KinRaiDee is being built as a Kitchen Operating System. It should help users manage ingredients, decide what to cook, reduce food waste, understand actual consumption, plan shopping, and eventually work with an optional intelligent cooking agent.

## Product goal

The long-term product should understand:

- what food exists in the household;
- quantities, units, categories, and expiry urgency;
- which recipes are possible now;
- which ingredients should be used first;
- what the household cooked previously;
- what should be bought next;
- nutrition, cost, repetition, and household preferences;
- how an AI agent can assist without becoming a required cloud dependency.

## Core product loop

```text
Pantry
  -> Recommendation
  -> Recipe selection
  -> Serving adjustment
  -> Cooking completion
  -> Pantry deduction
  -> Cooking history
  -> Future planning
```

This loop is the current product backbone. New features should strengthen it rather than create disconnected screens.

## Current application status

The product is in an early but structurally meaningful stage. The estimated feature completion is approximately 68%, while the broader Kitchen OS vision is approximately 55% defined or implemented.

### Completed capability groups

#### Pantry

- ingredient inventory management;
- categories and search;
- favourites;
- expiry-related information;
- quantities and units;
- transaction-based quantity updates.

#### Recommendation

- pantry-aware recommendations;
- hero ingredient selection;
- recipe coverage analysis;
- use-soon prioritisation;
- smart recommendation improvements.

#### Recipe

- recipe detail flow;
- serving adjustment;
- ingredient quantity scaling;
- recipe coverage against pantry quantities.

#### Cooking and pantry deduction

- completion flow;
- automatic pantry deduction;
- quantity transaction validation;
- rejection on missing ingredients, changed units, or insufficient stock;
- completion events published to the application shell.

#### Cooking history

A persistent cooking history feature was added in branch:

`feature/cooking-history-4.8.1`

Latest known commit:

`d8631869bb7e0eb18a9cc136189006212f8737dd`

History includes:

- completed, adjusted, and cancelled states;
- actual usage editing;
- cancellation;
- persistent storage;
- delta-based pantry correction;
- preserved adjusted quantity when cancelling an adjusted record.

### Important history design rule

History editing uses **deltas**, not pantry overwrites.

Example:

```text
Original usage: 4
Edited usage:   3
Pantry change: +1
```

```text
Original usage: 4
Edited usage:   5
Pantry change: -1
```

A history correction must never reset pantry stock to an assumed historical value. It applies only the difference between the old and corrected cooking usage.

## Current branch and repository workflow

Application repository local paths previously used:

```text
Repository: D:\Dev\Projects\KinRaiDee
Flutter app: D:\Dev\Projects\KinRaiDee\apps\mobile
```

GitHub is the source of truth.

Current development branch at handover:

`feature/cooking-history-4.8.1`

The branch was not yet declared merged at the time of handover. Inspect repository state before continuing.

## Existing validation workflow

The owner runs locally:

```bash
flutter analyze
flutter test
flutter run -d web-server
```

No branch should be merged merely because code was generated. The owner validates the product locally first.

## Known issue to inspect first

After the cooking history work, `RecipeDetailPage` may still contain an obsolete local snackbar while `MainShell` owns the global completion snackbar. This could cause duplicate snackbar behaviour.

Before starting a large new feature:

1. inspect the completion flow;
2. confirm whether two snackbars can appear;
3. remove obsolete local completion UI if duplicated;
4. add or update tests where practical.

## Current architecture direction

The application has providers, storage services, domain models, planners, and Flutter UI. The intended direction is modular domain engines:

```text
pantry_engine
recipe_engine
recommendation_engine
shopping_engine
nutrition_engine
ai_engine
```

Do not split packages prematurely merely to match this diagram. Extract only when boundaries are stable and the change reduces coupling rather than moving files cosmetically.

## Engineering rules

1. Business logic must not live in widgets.
2. Providers coordinate state; they should not become large algorithm containers.
3. Storage services must not depend on UI.
4. Domain rules should be testable without rendering Flutter screens.
5. Avoid duplicated calculations across recipe, pantry, and history flows.
6. Never bypass transaction validation to make a demo pass.
7. Prefer complete file replacements for implementation handoff when requested by the owner.
8. Do not merge or open a production PR until local validation is confirmed by the owner.
9. Significant architecture changes require CTO review and normally an ADR.
10. Documentation must be updated when product behaviour or architecture meaningfully changes.

## Immediate next work

### Step 0 — Stabilisation

Before adding the next large feature:

- inspect the current application branch and merge status;
- run analysis and tests;
- verify cooking history edit and cancellation behaviour;
- verify normal cooking records history exactly once;
- verify history edits do not create new history entries;
- verify cancellation cannot be repeated incorrectly;
- inspect the possible duplicate RecipeDetailPage snackbar.

### Step 1 — Shopping foundation

The next recommended major capability is Shopping.

The first shopping milestone should be deliberately narrow:

- manually add shopping items;
- generate missing ingredients from a selected recipe;
- preserve ingredient quantity and unit;
- prevent obvious duplicate entries;
- mark an item purchased;
- optionally transfer purchased quantities into Pantry through an explicit confirmed action;
- work fully offline;
- persist locally;
- include unit tests for generation and merge rules.

Do not begin with retailer integrations, price scraping, shared cloud lists, or AI purchasing.

### Step 2 — Ingredient substitution

After shopping is stable:

- represent substitution knowledge separately from UI;
- distinguish exact replacement, acceptable alternative, and flavour-changing alternative;
- validate units and conversion assumptions;
- expose substitution to recipe detail and recommendation without coupling it directly to widgets.

### Step 3 — Nutrition and meal planning

Nutrition should be introduced only after ingredient identities and quantities are reliable enough to support calculations. Meal planning should consume pantry, recipe, history, shopping, and later nutrition data through defined interfaces.

## AI direction

AI is optional and must not be the source of truth.

Future agent tools may include:

- Inventory Tool;
- Recipe Tool;
- History Tool;
- Shopping Tool;
- Nutrition Tool;
- Expiration Tool;
- Budget Tool.

The AI layer must not read Hive or another database directly. It should operate through explicit tools or domain interfaces, allowing storage replacement and policy enforcement.

Potential local model families may include small Gemma, Phi, Qwen, or Llama variants, but model selection is not approved yet. Do not add model dependencies until a measured RFC exists.

## Platform direction

Flutter allows most business logic and screens to be shared across Android and iOS. Android and web are the current practical validation platforms because the owner does not currently have a Mac.

iOS must eventually receive dedicated validation for:

- navigation and swipe-back behaviour;
- safe areas and keyboard handling;
- permissions;
- storage and backup behaviour;
- notification behaviour;
- build, signing, and store requirements.

## Definition of Done

A task is complete only when:

- behaviour matches written acceptance criteria;
- `flutter analyze` passes;
- `flutter test` passes;
- no known regression is introduced;
- tests cover important domain rules;
- relevant documentation is updated;
- commits describe the actual change;
- the owner validates the build before merge.

## Working relationship

- The CEO / Product Owner decides what user value and business direction matter.
- The CTO converts that direction into roadmap, constraints, architecture, specifications, and review decisions.
- Codex decides how to implement the approved work cleanly, provides tests, and reports trade-offs or blockers rather than silently changing direction.

When requirements conflict with existing architecture, stop and report the conflict. Do not hide it behind a quick implementation.

---

Status: Active handover  
Owner: CTO Office  
Last updated: 2026-07-25  
Next review: after the cooking-history branch is merged or superseded

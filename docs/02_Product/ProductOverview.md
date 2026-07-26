# Product Overview

## Product statement

KinRaiDee is a pantry-centred cooking platform that turns household food data into useful daily decisions.

It is not primarily a recipe catalogue. Recipes are one capability inside a broader operating loop that begins with what the user actually owns and continues through cooking, inventory updates, history, shopping, planning, and optional AI assistance.

## Primary user problem

People often have food at home but still do not know what to cook. They buy duplicates, forget ingredients, allow food to expire, and repeatedly spend time reconstructing information that their kitchen system should already know.

Search engines can return thousands of recipes, but they normally do not know:

- which ingredients are actually available;
- their remaining quantities;
- which items should be used soon;
- what the household recently cooked;
- whether missing ingredients are worth purchasing;
- the user's budget or nutrition goals.

KinRaiDee should reduce that decision burden.

## Target users

The initial target user is a household cook who:

- keeps ingredients at home;
- cooks several times per week;
- wants to reduce waste and unnecessary purchases;
- is willing to maintain a lightweight pantry record;
- prefers useful recommendations over browsing large recipe libraries.

Later audiences may include families, health-conscious users, budget-focused households, and users who need shared pantry planning.

## Core journey

```text
Acquire ingredients
  -> Add or update Pantry
  -> Track quantity and expiry
  -> Receive useful recommendations
  -> Select and scale a recipe
  -> Cook
  -> Deduct actual usage
  -> Record history
  -> Correct mistakes when needed
  -> Plan shopping and future meals
```

## Current product capabilities

- pantry inventory;
- search, category, favourite, quantity, unit, and expiry-related data;
- pantry-aware recipe recommendation;
- use-soon and coverage signals;
- recipe serving adjustment;
- automatic pantry deduction after cooking;
- persistent cooking history;
- history adjustment and cancellation using quantity deltas.

## Product boundaries

KinRaiDee should not become:

- a generic social media feed;
- a recipe content farm;
- an AI chatbot with no reliable household data;
- a retailer marketplace before the kitchen workflow is mature;
- a cloud-only product that stops working without connectivity.

## Product success signals

Early product success should be measured through behaviour rather than feature count:

- recommendations lead to completed cooking sessions;
- ingredients marked use-soon are consumed before expiry;
- pantry quantities remain trustworthy after cooking and corrections;
- users create fewer duplicate shopping entries;
- the time from opening the app to choosing a meal decreases;
- users return because the app remembers useful kitchen context.

## Product sequencing principle

Capabilities should be built in dependency order:

1. reliable pantry data;
2. useful recommendations and recipes;
3. trustworthy consumption history;
4. shopping and substitution;
5. nutrition, planning, and budgeting;
6. optional AI reasoning;
7. cloud and household collaboration.

Skipping foundational reliability to reach impressive AI features would weaken the product.

---

Status: Active  
Owner: Product and CTO Office  
Last updated: 2026-07-25

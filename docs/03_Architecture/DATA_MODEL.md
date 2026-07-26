# Data Model Overview

This document defines conceptual entities and invariants. Exact field names and adapters must be verified against the source repository.

## Ingredient

Canonical ingredient identity used across pantry, recipes, shopping, nutrition, and AI tools.

## Pantry Item

Represents an ingredient currently available.

Key concepts:

- ingredient identity
- current quantity
- unit
- optional expiry or freshness context
- metadata needed for recommendation and display

## Recipe

Represents a cookable dish.

Key concepts:

- recipe identity and title
- ingredient requirements
- preparation steps
- base serving count
- metadata used for search and recommendation

## Recipe Ingredient Requirement

Connects a recipe to an ingredient and defines the amount required for the recipe's base serving count.

## Cooking History Record

Represents a completed cooking event.

It must retain enough information to:

- display what was cooked;
- show when and at what serving adjustment;
- explain pantry deductions;
- restore the correct amounts when cancelled;
- preserve historical truth after recipe or pantry data later changes.

## Shopping Item

Planned entity for Shopping Foundation.

Expected concepts:

- ingredient or free-text identity;
- requested quantity and unit;
- source or reason;
- lifecycle state;
- created and completed timestamps;
- optional relationship to a recipe or pantry shortage.

The exact schema requires an approved Shopping specification and ADR.

## Model rules

- Persistent models must not leak directly into UI business logic.
- History records should use snapshots where later source edits could corrupt historical meaning.
- Unit conversion must be explicit; quantities with incompatible units must not be silently combined.
- Migrations and backward compatibility must be considered before changing stored models.

# Product and Engineering Roadmap

## Roadmap principle

The roadmap follows dependency order. KinRaiDee should first make pantry data reliable, then turn that data into useful cooking and shopping decisions, and only afterward add nutrition, planning, AI, cloud, and collaboration.

Dates are directional until validated by delivery velocity and user feedback.

## Current foundation

Completed or substantially implemented:

- pantry management;
- pantry-aware recommendations;
- use-soon and coverage logic;
- recipe serving scaling;
- cooking completion;
- pantry deduction;
- persistent cooking history;
- history adjustment and cancellation using deltas.

## Immediate milestone — Stabilise cooking history

Before beginning a major new module:

- confirm the current application branch and merge state;
- run Flutter analysis and tests;
- verify normal cooking creates one history entry;
- verify history corrections do not create additional history entries;
- verify adjusted cancellation returns the correct amount;
- inspect and remove any duplicate completion snackbar flow.

## Milestone 1 — Shopping foundation

Goal: convert recipe gaps and user intent into a trustworthy offline shopping list.

Scope:

- manual shopping items;
- recipe missing-ingredient generation;
- quantity and unit preservation;
- duplicate merge rules;
- purchased state;
- confirmed transfer to Pantry;
- local persistence;
- domain tests.

Out of scope:

- retailer integration;
- price scraping;
- automatic purchasing;
- shared cloud lists;
- AI shopping decisions.

## Milestone 2 — Ingredient substitution

Goal: help users continue cooking when an ingredient is missing.

Scope:

- substitution domain model;
- replacement confidence or impact level;
- unit and conversion constraints;
- recipe-detail integration;
- recommendation integration where useful.

## Milestone 3 — Nutrition foundation

Goal: provide understandable nutrition estimates based on reliable ingredient identity and quantity.

Scope:

- nutrition data model;
- source metadata;
- serving-based calculations;
- uncertainty and missing-data handling;
- no unsupported medical claims.

## Milestone 4 — Meal planning

Goal: plan upcoming meals using pantry, history, shopping, recipe, and nutrition data.

Scope:

- calendar-like meal plan;
- recipe assignment;
- pantry coverage forecast;
- shopping impact;
- repeated-meal awareness.

## Milestone 5 — Budget and forecast

Goal: help households understand future food needs and spending.

Scope:

- estimated shopping cost inputs;
- consumption trends;
- pantry depletion forecast;
- budget-aware suggestions.

## Milestone 6 — AI Chef alpha

Goal: introduce read-only, grounded AI assistance through explicit domain tools.

Scope:

- tool contracts;
- grounded recipe and pantry reasoning;
- explicit uncertainty;
- no silent data mutation;
- evaluation dataset and quality criteria.

## Milestone 7 — Vision and input automation

Goal: reduce pantry data-entry effort after the core system is trustworthy.

Potential scope:

- barcode scanning;
- receipt OCR;
- ingredient photo assistance;
- confirmation before data mutation.

## Milestone 8 — Cloud sync and family sharing

Goal: support multiple devices and households without weakening offline ownership.

Requires prior RFCs for:

- identity;
- sync conflict strategy;
- encryption and privacy;
- migration;
- offline reconciliation;
- household roles and permissions.

## North-star destination

```text
Pantry Engine
Recipe Engine
Recommendation Engine
Shopping Engine
Nutrition Engine
Planning and Budget Engine
AI Tool Layer
Optional Local AI
Cloud Sync
Family Kitchen
```

The destination is a Kitchen Operating System, but each milestone must deliver practical standalone value.

---

Status: Active direction  
Owner: CEO and CTO Office  
Last updated: 2026-07-25  
Next review: after Shopping foundation specification

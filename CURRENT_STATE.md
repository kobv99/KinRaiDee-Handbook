# Current State

_Last updated: 2026-07-26_

This document describes the current product and engineering state. Update it whenever the implementation reality changes.

## Repositories

- Source code: `kobv99/KinRaiDee`
- Handbook: `kobv99/KinRaiDee-Handbook`
- Mobile app path: `apps/mobile`
- GitHub is the source of truth.

## Current product stage

KinRaiDee has a functional offline-first cooking workflow and is moving from cooking-history stabilisation toward the Shopping Foundation milestone.

## Implemented capabilities

- Pantry management
- Recipe management
- Smart recommendations
- Recipe serving adjustment
- Use-soon recommendations
- Recipe coverage analysis
- Pantry deduction after cooking
- Cooking history
- Cooking-history cancellation and pantry restoration

## Latest known source commit

`d8631869bb7e0eb18a9cc136189006212f8737dd`

Commit message:

`fix(history): retain returned amount when cancelling an adjusted record`

This is a historical handover reference and must be verified against the source repository before beginning implementation work.

## Current milestone

**Shopping Foundation**

The immediate product direction is to stabilise existing cooking-history behaviour and then establish the Shopping domain without breaking offline-first guarantees or existing pantry transactions.

## Current focus

1. Verify the current source branch and test state.
2. Confirm that cooking-history cancellation and pantry restoration are stable.
3. Define and implement the Shopping Foundation from an approved feature specification.

## Known risks

- Handbook state can become stale if source changes are not reflected here.
- Business rules currently encoded only in source may be missed by a new AI engineer.
- Shopping must not be coupled directly to UI or Hive storage.
- Pantry quantities, history records, and cancellation behaviour require transaction-safe updates.

## Planned capabilities

- Shopping Foundation
- Nutrition
- Meal planning
- AI Chef and tool-based agents
- Vision-assisted ingredient capture
- Optional cloud synchronisation
- Family sharing

## Status rule

This file must reflect verified reality. When handbook and source conflict, stop implementation, inspect the source repository, and update this document before continuing.

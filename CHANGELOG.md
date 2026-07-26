# Changelog

All meaningful changes to the KinRaiDee Handbook are recorded here.

## [Unreleased]

### Integrated

- merged Architecture Audit + RFC-0003 through `KinRaiDee-Handbook#2` at `26275eda86959c1ed9b469e06e2dcbff5856f599`;
- merged Sprint S-001 Transaction Engine through `KinRaiDee#1` at `78be1dd07c99af98606a7f1a0937d75ef9fcdfb6`;
- rebased onto the updated source baseline and merged Sprint S-002 Canonical Ingredient System through `KinRaiDee#2` at `77d0953ba0ccae976ccca678e4bd15652e11572b`;
- merged integration persistence-boundary stabilization through `KinRaiDee#3` at `6bba09275bedf76944779f59a28042ce11727d11`.

### Added

- final Shopping Foundation readiness assessment;
- durable, revisioned, crash-recoverable Pantry + Cooking History transaction engine;
- idempotent retry, commit, undo, and cancel behavior with all-or-nothing multi-item mutations;
- canonical ingredient registry, unit contract, deterministic normalization, and automatic local-data migration;
- repository-backed recipe preference persistence and durable-snapshot Cooking History publication;
- regression tests for presentation persistence boundaries;
- draft RFC-0003 for transaction safety, inventory consistency, idempotency, and restart recovery;
- complete architecture readiness audit for application commit `d8631869`;
- current dependency graphs for application, Pantry, Recipe, recommendations, Cooking History, undo/cancel, and persistence;
- data model and Shopping compatibility review;
- prioritized P0-P3 technical debt register with evidence and Shopping blockers;
- staged refactor proposal and architecture risk register;
- handbook landing page;
- onboarding guide;
- complete Codex project handover;
- company vision, mission, principles, and constitution;
- product overview and sequencing principles;
- architecture overview and AI boundary;
- engineering standards, review checklist, and Definition of Done;
- AI vision;
- sequenced product roadmap;
- ADR and RFC processes;
- ADR and RFC templates;
- initial accepted ADRs for offline-first core and AI tool-layer access.

### Changed

- changed Shopping Foundation readiness from **NOT READY** to **READY WITH MINOR DEFERRED WORK**, pending explicit CTO approval to begin;
- established source `main` at `6bba09275bedf76944779f59a28042ce11727d11` as the stable baseline;
- recorded a green final gate: format pass, analyzer 0 issues, 108 tests pass, and 80.77% line coverage;
- updated `CURRENT_STATE.md` and `NOW.md` to reflect the integrated architecture and remaining non-blocking technical debt;
- marked Shopping Foundation **NOT READY** pending transaction safety, canonical identity/unit decisions, repository compliance, source-branch stabilisation, and green automated gates;
- updated `CURRENT_STATE.md` with verified source commits, audit results, and current risks;
- updated `NOW.md` to prioritize architecture-readiness stabilisation before Shopping.

## [0.1.0-foundation] - 2026-07-25

Initial handbook foundation prepared for review.

# Engineering Handbook

## Engineering objective

KinRaiDee should evolve quickly without sacrificing pantry correctness, maintainability, or the ability to work offline. Engineering quality is therefore defined by reliable behaviour, explicit domain rules, useful tests, and clear change history—not by code volume or delivery speed alone.

## Coding standards

### Keep business logic out of widgets

Widgets render state and collect user actions. Reusable calculations, validation, planning, and inventory rules belong in domain components.

### Keep providers focused

Providers may coordinate state and dependencies, but complex recommendation, merge, deduction, correction, or conversion algorithms should live in dedicated testable classes.

### Make mutations explicit

Every inventory change should communicate:

- what caused the change;
- which ingredient is affected;
- quantity and unit;
- validation result;
- whether history should be recorded;
- whether a user-facing completion event should be published.

### Prefer small domain components

Use planners, validators, policies, and value objects when they make rules clearer. Avoid creating abstractions with no stable responsibility.

### Preserve null safety and readable naming

Names should communicate product meaning. Avoid generic classes such as `Helper`, `Manager`, or `Utils` when a more precise domain name exists.

### Avoid hidden coupling

UI should not depend on storage implementation. AI should not depend on Hive. Recommendation should not modify pantry state. History correction should not recreate a cooking completion event.

## Git workflow

Application work normally follows:

```text
main or approved base
  -> feature/<descriptive-name>
  -> local validation
  -> CTO review
  -> pull request
  -> merge after owner approval
```

The handbook uses documentation branches and pull requests for meaningful changes.

### Commit messages

Use concise conventional-style commits:

```text
feat(shopping): generate missing recipe ingredients
fix(history): prevent duplicate cancellation
refactor(pantry): extract quantity validation policy
test(recommendation): cover use-soon ranking
docs(architecture): record AI tool boundary
```

## Required validation

For Flutter application changes:

```bash
flutter analyze
flutter test
flutter run -d web-server
```

The owner performs final local product validation before merge.

## Definition of Done

A task is complete only when:

- acceptance criteria are met;
- important domain rules have tests;
- analysis and tests pass;
- no known regression remains hidden;
- architecture is not degraded by duplicated or misplaced logic;
- user-visible error states are handled;
- relevant handbook or ADR documentation is updated;
- commits explain the real change;
- the owner validates before merge.

## Review checklist

### Product

- Does the change solve a real kitchen problem?
- Is the workflow understandable without explanation?
- Does it strengthen the pantry-to-cooking loop?

### Data correctness

- Are units and quantities validated?
- Are inventory mutations auditable?
- Can retries or repeated taps duplicate effects?
- Are historical edits delta-based where required?

### Architecture

- Is business logic outside widgets?
- Is storage isolated?
- Are provider responsibilities still manageable?
- Is a new dependency genuinely necessary?
- Does the change require an ADR?

### Quality

- Are tests meaningful rather than superficial?
- Are errors and edge cases explicit?
- Is dead or obsolete code removed?
- Is documentation consistent with implementation?

## Prohibited shortcuts

Do not:

- bypass validation to make a feature appear complete;
- add duplicate logic to avoid understanding existing behaviour;
- silently change architecture during a feature ticket;
- merge untested generated code;
- use AI output as authoritative pantry data;
- add cloud dependencies to an offline core flow without approval;
- leave temporary hacks undocumented.

---

Status: Active  
Owner: CTO Office  
Last updated: 2026-07-25

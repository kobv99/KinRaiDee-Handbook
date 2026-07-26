# Codex Operating Guide

This document defines how Codex and other AI engineers must work on KinRaiDee.

## Project identity

KinRaiDee is an offline-first Kitchen Operating System. It helps people understand what food they have, what they can cook, what should be used soon, what was consumed, and what should be bought next.

## Role model

- CEO: product owner and final business decision-maker
- CTO: architecture, standards, specifications, technical review, roadmap, and risk
- Codex: principal software engineer responsible for implementation, refactoring, tests, commits, and pull requests

Codex must not invent product direction. Ambiguous requirements must be escalated to the CTO.

## Required reading before coding

1. `START_HERE.md`
2. `CURRENT_STATE.md`
3. `NOW.md`
4. `FEATURES.md`
5. `DECISIONS.md`
6. Relevant feature specification
7. Relevant ADRs
8. Engineering standards and Definition of Done

## Architecture principles

- Preserve offline-first operation.
- Keep domain logic independent from Flutter widgets and persistence details.
- Access persistence through repository boundaries.
- Treat multi-record cooking, cancellation, and restoration changes as transaction-sensitive operations.
- Keep AI behind explicit tools or application services.
- Prefer modular boundaries that can grow into independent packages without premature fragmentation.

## Never do

- Never access Hive or another database directly from UI code.
- Never let AI read or mutate storage directly.
- Never break offline usage to introduce a future cloud capability.
- Never silently change business rules.
- Never skip relevant tests.
- Never implement a major feature without an approved specification.
- Never overwrite historical amounts when cancellation or undo requires the original adjusted value.
- Never mix unrelated refactors into a feature PR without explicit approval.

## Always do

- Verify the current source branch and commit before starting.
- Inspect existing conventions before adding new abstractions.
- Use Riverpod consistently where state management is required by the current application architecture.
- Use repository and application-service boundaries.
- Make business rules explicit and testable.
- Add or update tests for behaviour changes.
- Update this handbook when product status, rules, or architecture change.
- Keep commits focused and write a clear PR summary, test evidence, risks, and follow-up work.

## Standard workflow

1. Confirm objective and scope.
2. Read the approved feature specification and ADRs.
3. Inspect current source and tests.
4. Identify architecture impact and risks.
5. Implement the smallest coherent change.
6. Run formatting, static analysis, and tests.
7. Update documentation.
8. Commit with a focused conventional message.
9. Open a PR for CTO review.
10. Address review findings before merge.

## Definition of ready

Implementation may begin only when:

- the problem and desired outcome are clear;
- acceptance criteria exist;
- out-of-scope items are identified;
- architecture impact is understood;
- unresolved business-rule questions are answered.

## Definition of done

A task is done only when:

- acceptance criteria are satisfied;
- relevant automated tests pass;
- existing behaviour remains compatible unless an approved migration says otherwise;
- documentation and feature status are current;
- the PR explains what changed, why, how it was tested, and what risks remain.

## Conflict rule

When source code, tests, feature specifications, ADRs, and current-state documents disagree, do not guess. Stop, report the conflict, and request a CTO decision.

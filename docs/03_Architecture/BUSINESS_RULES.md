# Business Rules

These rules are binding unless superseded by an accepted ADR or approved feature specification.

## Pantry

- Pantry quantities must not become negative through normal user actions.
- Quantity and unit changes must be validated before persistence.
- Incompatible units must not be silently aggregated.
- UI code must not mutate persistence directly.

## Recipe and recommendation

- Serving adjustments must scale ingredient requirements consistently.
- Recipe coverage compares required amounts with available pantry amounts.
- Use-soon prioritisation must use freshness or expiry context without corrupting pantry data.
- Recommendation output is advisory; it does not mutate pantry state.

## Cooking

- Completing a cooking action creates a durable history record.
- Pantry deduction and history creation are one logical transaction.
- Deduction must use the actual serving-adjusted amount.
- A partial failure must not leave pantry and history in conflicting states.

## Cancellation and restoration

- Cancelling a history record restores the amounts actually deducted for that event.
- Restoration must use historical snapshots, not recalculated current recipe values.
- A record must not be restored more than once.
- Cancellation state must remain auditable.
- Adjusted returned amounts must be preserved when a cancellation record is retained.

## Shopping

- Shopping items represent acquisition intent; they do not deduct pantry quantities.
- Shopping candidates may be derived from recipe shortages, low pantry levels, or explicit user input.
- Generated candidates must remain distinguishable from manually entered items.
- Completing a shopping item must not automatically alter pantry until that behaviour is explicitly specified.

## AI

- AI may only act through approved tools or application services.
- AI must not access Hive or another datastore directly.
- AI suggestions must not silently commit destructive mutations.
- Core cooking and pantry workflows must remain usable without AI or network access.

## Conflict handling

When a requested change conflicts with these rules, implementation stops until the CTO approves a rule change and records the decision.

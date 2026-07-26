# Transaction-Safe Domain Operations

KinRaiDee uses local persistence, but multi-record business operations must still behave as one logical transaction.

## Operations requiring transaction safety

- Cooking: deduct pantry and create history.
- Cancellation: restore pantry and mark or retain cancellation state.
- Future shopping-to-pantry conversion.
- Future migration affecting related records.

## Required properties

### Atomic business outcome

The user must observe either a complete success or a safe failure. Pantry and history must not disagree.

### Idempotency

Retrying or reopening an operation must not duplicate deductions, restoration, or history records.

### Historical snapshots

Undo and cancellation use the values recorded at execution time, including serving-adjusted amounts, rather than recalculating from mutable current recipe data.

### Recovery

If the storage technology cannot provide a true cross-record transaction, the application service must use a documented compensation or recovery strategy and test failure paths.

## Cooking flow

1. Validate recipe and pantry inputs.
2. Calculate adjusted ingredient requirements.
3. Capture the exact planned deductions.
4. Apply deductions through repository methods.
5. Save a history snapshot containing the actual deductions.
6. On failure, compensate or restore any completed partial changes.
7. Return a single outcome to the UI.

## Cancellation flow

1. Verify the history record exists and is eligible for cancellation.
2. Verify it has not already been restored.
3. Restore the recorded actual deduction amounts.
4. Persist cancellation state without losing the historical snapshot.
5. Return a single outcome to the UI.

## Testing requirements

Tests must cover:

- normal success;
- insufficient pantry quantity;
- failure after a partial write;
- repeated cancellation attempt;
- serving-adjusted deduction and restoration;
- recipe changes after history creation;
- application restart between related operations when relevant.

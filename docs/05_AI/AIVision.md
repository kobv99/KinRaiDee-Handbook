# AI Vision

## Position

AI is an optional reasoning layer over trusted KinRaiDee data. It is not the product's source of truth and must not be required for basic pantry, cooking, history, or shopping workflows.

## Long-term goal

The future KinRaiDee agent should help answer questions such as:

- What should I cook today?
- Which ingredients should be used first?
- What can replace a missing ingredient?
- What should I buy for the coming week?
- Am I repeating the same meals too often?
- How can I stay within a food budget?
- How can I improve nutrition using what is already available?

Useful answers must be grounded in household data and deterministic domain rules.

## Tool-based agent architecture

A future agent may call explicit tools:

```text
Inventory Tool
Recipe Tool
History Tool
Shopping Tool
Nutrition Tool
Expiration Tool
Budget Tool
```

Tools enforce permissions, validation, and stable data contracts. This prevents the model from depending directly on Hive, a future SQL database, or cloud storage.

## Safety and trust rules

AI must not:

- silently change pantry quantities;
- invent ingredients and present them as owned;
- bypass unit or stock validation;
- make medical nutrition claims beyond supported data;
- hide uncertainty;
- require cloud connectivity for the core app.

Actions that mutate household data require an explicit product workflow and user confirmation.

## Offline strategy

On-device models are a preferred future option for privacy and offline use. Potential small-model families may include Gemma, Phi, Qwen, and Llama variants, but no model family is approved yet.

Model adoption requires an RFC covering:

- device support;
- download size;
- memory and latency;
- answer quality;
- safety limitations;
- licensing;
- update and removal strategy.

AI packs should be optional downloads rather than increasing the core application size for every user.

## Knowledge strategy

The agent should combine:

- deterministic pantry and transaction data;
- curated recipe knowledge;
- substitution rules;
- nutrition data with source quality metadata;
- household preferences and history;
- model-generated reasoning that remains clearly distinguishable from stored facts.

## Delivery stages

1. Deterministic smart recommendations.
2. Tool interfaces around existing domains.
3. AI Chef alpha for read-only recommendations.
4. Confirmed actions through tools.
5. Optional local model pack.
6. Multimodal assistance such as camera and voice after measured validation.

---

Status: Proposed direction  
Owner: CTO Office  
Last updated: 2026-07-25

# Architecture Decision Records

Architecture Decision Records capture important technical decisions, the context in which they were made, alternatives considered, and consequences accepted.

## Status values

- Proposed
- Accepted
- Superseded
- Rejected
- Deprecated

## Current ADR index

- [ADR-001: Offline-first core](ADR-001-Offline-First.md)
- [ADR-002: AI accesses product data through tools](ADR-002-AI-Tool-Layer.md)

## When an ADR is required

Create an ADR for changes involving:

- persistence technology;
- package or service boundaries;
- cloud sync;
- authentication or household roles;
- AI runtime or model distribution;
- breaking data migrations;
- new source-of-truth rules;
- platform-specific background processing;
- decisions that future contributors may otherwise reverse without understanding the original trade-off.

Use the template in [`templates/ADR-Template.md`](../../templates/ADR-Template.md).

---

Status: Active  
Owner: CTO Office  
Last updated: 2026-07-25

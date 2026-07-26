# Decision Index

This file is the quick index for architectural and product-engineering decisions. Detailed rationale belongs in ADR documents.

| ID | Decision | Status | Reference |
|---|---|---|---|
| ADR-001 | KinRaiDee is offline first | Accepted | `docs/07_ADR/ADR-001-offline-first.md` |
| ADR-002 | AI operates through an explicit tool layer | Accepted | `docs/07_ADR/ADR-002-ai-tool-layer.md` |
| ADR-003 | Repository boundaries govern persistence access | Proposed | To be written |
| ADR-004 | Transaction-safe pantry and history mutations | Proposed | To be written |
| ADR-005 | Shopping is a first-class domain | Proposed | To be written before implementation |
| ADR-006 | Nutrition follows Shopping stabilisation | Planned | Roadmap decision |
| ADR-007 | Cloud capabilities remain optional | Planned | Future ADR |

## Decision rules

- Accepted ADRs are binding until superseded.
- Proposed ADRs must not be treated as final architecture.
- Major implementation work that creates a new persistent model, package boundary, integration, or irreversible dependency requires an ADR.
- When a decision changes, preserve history by marking the old ADR superseded instead of rewriting it as though the earlier decision never existed.

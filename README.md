# KinRaiDee Handbook

> The official product, architecture, engineering, and AI handbook for the KinRaiDee AI Cooking Platform.

KinRaiDee is not being built as another recipe application. It is being designed as a **Kitchen Operating System**: an offline-first, privacy-conscious platform that helps people understand what food they have, decide what to cook, reduce waste, plan purchases, and eventually work with an intelligent kitchen agent.

This repository is the **single source of truth** for the intent behind the product. The application repository explains how the software works; this handbook explains why it exists, how it should evolve, and which engineering principles must remain stable as the system grows.

## Start here

1. [Welcome and onboarding](docs/00_Welcome/README.md)
2. [Company foundation](docs/01_Company/README.md)
3. [Product overview](docs/02_Product/ProductOverview.md)
4. [Architecture overview](docs/03_Architecture/Architecture.md)
5. [Engineering standards](docs/04_Engineering/README.md)
6. [AI strategy](docs/05_AI/AIVision.md)
7. [Roadmap](docs/06_Roadmap/Roadmap.md)
8. [Architecture decisions](docs/07_ADR/README.md)
9. [RFC process](docs/08_RFC/README.md)
10. [Current handover for Codex](docs/00_Welcome/CodexHandover.md)

## Current product state

The core operating loop is already established:

```text
Pantry
  -> Recommendation
  -> Recipe
  -> Cooking
  -> Pantry deduction
  -> Cooking history
```

The current mobile product already includes pantry management, recommendation logic, recipe serving adjustment, pantry deduction, and persistent cooking history. The next major product expansion is the shopping workflow, followed by ingredient substitution, nutrition, meal planning, budgeting, and AI-assisted cooking.

## Team operating model

- **CEO / Product Owner:** owns vision, priorities, user validation, and final product decisions.
- **CTO:** owns roadmap, architecture, engineering standards, technical risk, and documentation.
- **Principal Software Engineer (Codex):** implements approved specifications, tests changes, and prepares reviewable branches and pull requests.

No significant feature should begin as an unstructured coding task. Product intent and acceptance criteria must be clear first; architecture impact must be assessed; implementation follows afterward.

## Core principles

- Offline first
- Privacy first
- User data ownership
- AI as an assistant, not a dependency
- Business logic outside UI widgets
- Small, testable domain components
- Documentation changes together with meaningful product changes

## Related repository

Application source code: [kobv99/KinRaiDee](https://github.com/kobv99/KinRaiDee)

## Handbook status

- Version: `0.1.0-foundation`
- Status: Active development
- Owner: CTO Office
- Last updated: 2026-07-25

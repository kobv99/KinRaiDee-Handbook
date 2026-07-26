# Project Map

## Repositories

```text
kobv99/KinRaiDee
└── apps/mobile

kobv99/KinRaiDee-Handbook
└── product, architecture, engineering, AI, roadmap, ADR, RFC, and operating guidance
```

## Ownership

- `KinRaiDee` owns executable source, tests, assets, generated files, and application configuration.
- `KinRaiDee-Handbook` owns intent, business rules, architecture decisions, feature specifications, roadmap, and engineering governance.

## Expected application layers

The exact source tree must be verified before implementation. New work should preserve these conceptual boundaries:

```text
Presentation
  Flutter screens, widgets, controllers, providers

Application
  Use cases, orchestration, commands, queries, transaction coordination

Domain
  Entities, value objects, policies, business rules, repository interfaces

Infrastructure
  Hive adapters, local storage, platform services, future integrations
```

## Navigation rule

Before changing a module, locate:

1. its feature specification;
2. its domain rules;
3. its repository boundary;
4. its tests;
5. its applicable ADRs.

Do not invent directory names or move code merely to match this conceptual map. Refactoring the physical source structure requires a separate approved plan.

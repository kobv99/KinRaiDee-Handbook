# System Diagram

```mermaid
flowchart TB
    User --> UI[Flutter UI]
    UI --> State[Riverpod State and Controllers]
    State --> App[Application Services and Use Cases]
    App --> Domain[Domain Rules and Entities]
    App --> Repo[Repository Interfaces]
    Repo --> Local[Local Persistence Adapters]
    Local --> Hive[(Hive / Local Storage)]
    App --> Tools[AI Tool Layer]
    Tools --> AI[Optional AI Models]

    subgraph Core Domains
      Pantry
      Recipe
      Recommendation
      Cooking
      History
      Shopping
      Nutrition
    end

    Domain --> Pantry
    Domain --> Recipe
    Domain --> Recommendation
    Domain --> Cooking
    Domain --> History
    Domain --> Shopping
    Domain --> Nutrition
```

## Runtime principles

- Core workflows run locally without network access.
- Flutter UI communicates with application-facing state and use cases.
- Business rules remain independent from UI and storage technology.
- Repositories isolate persistence details.
- AI is optional and interacts through explicit tools.
- Future cloud services must complement, not replace, local operation.

## Cooking transaction flow

```mermaid
sequenceDiagram
    actor User
    participant UI
    participant Cooking as Cooking Service
    participant Pantry as Pantry Repository
    participant History as History Repository

    User->>UI: Confirm cooking
    UI->>Cooking: Cook recipe with adjusted servings
    Cooking->>Cooking: Validate requirements and calculate actual deductions
    Cooking->>Pantry: Apply pantry deductions
    Cooking->>History: Save immutable history snapshot
    alt all operations succeed
      Cooking-->>UI: Success
    else any operation fails
      Cooking->>Pantry: Restore or roll back changes
      Cooking-->>UI: Failure without inconsistent state
    end
```

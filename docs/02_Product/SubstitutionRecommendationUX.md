# Substitution Recommendation UX

## Product principle

> The application recommends. The user decides.

Ingredient substitutions are recommendations, not mandatory actions. They help a
user continue cooking when an ingredient is missing, but they must never become
a gate in the Recipe or Cooking journey.

## User-control requirements

The user must always be able to:

- accept a suggested substitute;
- ignore the recommendation and continue;
- collapse the recommendation;
- hide it from the main content area; and
- reopen it later.

Accepting a substitute is always optional. Viewing, ignoring, collapsing,
hiding, or reopening a recommendation must not mutate Pantry, Shopping, Recipe,
or Cooking data.

## Presentation contract

Substitutions appear as a collapsible recommendation card on Recipe detail.
The default presentation is compact so the recommendation does not permanently
consume space needed by the Recipe and Start Cooking workflow.

### Collapsed

```text
Suggested Substitutions (2)

▼ Expand
```

### Expanded

```text
Suggested Substitutions

Chicken Breast
      ↓
Chicken Thigh
Chicken Wing

[Accept]  [Ignore]  [Hide]
```

### Hidden

The full card is replaced by a small banner or chip that allows the user to
reopen the recommendations. The released area is returned immediately to the
Recipe content; no blank placeholder spacing remains.

## State behaviour

A collapsed or hidden state remains stable while the recommendation set is
unchanged. Navigation, ordinary widget rebuilds, and unrelated screen updates
must not force the expanded card back into view.

When the recommendation changes materially—for example because Pantry
availability changes, ranking changes, or a different substitute becomes
relevant—the interface may return to the compact collapsed state so the new
advice is discoverable without interrupting the user.

The recommendation identity must be deterministic. It should be derived from
the Recipe, original ingredient, ordered substitute candidates, relevant Pantry
availability, and recommendation evidence rather than transient widget state.

## Primary-workflow protection

The substitution panel must never:

- cover or disable Start Cooking;
- require an Accept, Ignore, or Close action before continuing;
- replace Recipe content with a modal decision;
- automatically apply a substitute;
- automatically add an item to Pantry or Shopping; or
- permanently reserve a large section of the screen.

The Recipe and Cooking workflow remains usable in every recommendation state,
including loading, unavailable, collapsed, expanded, hidden, ignored, and
accepted.

## Responsive layout

- Expanded recommendation content uses a bounded, internally scrollable area.
- Collapsing or hiding restores the available Recipe content area.
- Narrow screens and large text must not produce bottom overflow.
- Multiple substitute candidates must wrap or stack instead of compressing the
  primary workflow.
- Hidden state uses only a small reopen affordance.

## Accessibility and copy

Controls must have explicit labels and tooltips or semantic descriptions.
Copy must consistently describe substitutions as optional recommendations.
Avoid language that implies the original Recipe is invalid or that the user
must resolve the recommendation before cooking.

Preferred language communicates:

- why the substitute may help;
- what may change in flavor, texture, or method;
- whether it is already available in Pantry; and
- that the user may continue without accepting it.

## Acceptance criteria

1. A valid substitution appears as a compact collapsible card.
2. The user can expand, collapse, hide, and reopen the recommendation.
3. Hidden state remains while the recommendation identity is unchanged.
4. A materially changed recommendation returns as a compact collapsed card.
5. Hiding leaves no empty layout gap.
6. Start Cooking remains reachable and functional without accepting anything.
7. Accept records only the explicit user choice.
8. Ignore, collapse, hide, and reopen do not mutate domain data.
9. The expanded panel does not overflow at narrow mobile dimensions.
10. The product remains usable when recommendation loading or generation fails.

## Non-goals

This UX contract does not authorize:

- automatic ingredient replacement;
- automatic Pantry or Shopping mutation;
- forced substitution confirmation;
- modal recommendation gates; or
- AI-generated substitutions without the same deterministic safety and
  user-control rules.

---

Status: Active product contract  
Owner: Product and CTO Office  
Last updated: 2026-07-29

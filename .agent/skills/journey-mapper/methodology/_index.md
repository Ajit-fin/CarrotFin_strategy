# Journey Mapper — Methodology Index

> **Last updated:** 2025-04-15

---

## Journey Template

Use this template when defining a new user journey in `product-design/journey-catalog/`:

```markdown
# Journey: [Name]

> **Created:** YYYY-MM-DD  
> **Trigger:** [User-initiated | AI-initiated | Ambient]  
> **Priority:** [P0-P3]  
> **Related assumptions:** [IDs from assumptions-tracker.md]

## Entry Conditions
- User state requirements: [literacy, trust, life stage constraints]
- Trigger signal: [what activates this journey]

## Emotional Arc
[Entry state] → [Mid-journey state] → [Exit state]
Example: Curious → Uncertain → Confident → Committed

## Journey Map

| Step | Screen Type | Components | AI Role | User Action |
|---|---|---|---|---|
| 1 | [Gen/Static/Hybrid] | [component list] | [what AI does] | [what user does] |

## AI Decision Points
| Decision | Criteria | Path A | Path B |
|---|---|---|---|

## Adaptive Variants
### Variant: [context dimension]
[How the journey changes for different values of this dimension]

## Success Metric
[What constitutes completion — be specific and measurable]

## Abandonment Handling
[What happens if the user drops off — how does the AI recover or store state]
```

---

*Journeys should be reviewed quarterly for relevance. Archive journeys for deprecated features.*

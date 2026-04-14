# Strategy — Directory Guide

> **Purpose:** Strategic documents and analyses for CarrotFin.

---

## What Goes Here

- **Strategic analyses** — market positioning, competitive strategy, growth plans
- **Fundraising materials** — pitch decks, investor narratives, financial models
- **Go-to-market plans** — launch strategy, channel strategy, partnership evaluations
- **Strategic frameworks** — applied to CarrotFin's specific context

## What Does NOT Go Here

- **Decisions** → `decision-log/` (immutable records)
- **Knowledge/context** → `knowledge-base/` (growing, append-only)
- **Research artifacts** → `research/` (archived with freshness dates)
- **Product design** → `product-design/` (UX/UI specifications)

## Naming Convention

`YYYY-MM-DD-[topic-slug].md` — e.g., `2025-05-01-seed-fundraise-strategy.md`

## File Template

```markdown
# [Title]

> **Created:** YYYY-MM-DD  
> **Status:** Draft / In Review / Final  
> **Related assumptions:** [IDs from assumptions-tracker.md]  
> **Related decisions:** [filenames from decision-log/]

---

[Content]
```

---

*Files in this directory are working documents. When a strategic document leads to a decision, create an immutable entry in `decision-log/`.*

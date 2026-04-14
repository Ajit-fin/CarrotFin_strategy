# Journey Mapper — Skill Router

> **Skill type:** Methodology  
> **Invoked by:** `/ux-designer`, `/product`  
> **Last updated:** 2026-04-15

---

## Purpose

This skill provides the methodology for mapping CarrotFin user journeys. User journeys in CarrotFin are not linear screen flows — they are adaptive, AI-mediated experiences where the path depends on the user's context, behavior, and the AI's reasoning. This skill ensures journeys are defined with the right structure and appropriate adaptive considerations.

## When to Use This Skill

| Scenario | Action |
|---|---|
| Defining a new user journey | Follow the journey definition methodology below. |
| Evaluating an existing journey | Use the journey audit checklist to verify completeness. |
| Designing Journey ↔ AI handoffs | Reference the AI intervention opportunity framework. |
| Simple screen-level interaction design | Skip this skill — use `/ux-designer` directly. Journeys span multiple screens/interactions. |

## Directory Navigation

```
journey-mapper/
├── SKILL.md              ← You are here — routing guide
├── methodology/          ← Process definitions and templates
│   └── _index.md         ← Journey mapping methodology
└── resources/            ← Templates and reference materials
    └── _index.md         ← Resource inventory
```

## Journey Definition Framework

Every CarrotFin user journey must capture these elements:

### 1. User State Model
- **Entry conditions:** What user state dimensions trigger this journey? (literacy level, life stage, trust level, etc.)
- **Emotional arc:** What emotional states does the user move through? (curious → uncertain → confident → committed)
- **Exit conditions:** What constitutes journey completion vs. abandonment vs. pause?

### 2. Decision Points
- **User decisions:** Where does the user make explicit choices?
- **AI decisions:** Where does the AI choose between paths based on user context?
- **Handoff points:** Where does the modality shift between conversational and visual?

### 3. AI Intervention Opportunities
- **Proactive nudges:** Where can the AI surface this journey without user initiation?
- **Contextual triggers:** What signals (behavioral, temporal, financial) activate this journey?
- **Progressive disclosure gates:** Where does the AI reveal more complexity based on user readiness?

### 4. Adaptive Dimensions
- **Literacy-driven branching:** How does the journey simplify or deepen based on financial literacy?
- **Trust-gated stages:** Which journey stages require higher trust levels (and thus more data)?
- **Modality preference:** How does the journey shift for conversational-first vs. visual-first users?

## Journey Audit Checklist

- [ ] Trigger defined (user-initiated or AI-initiated)
- [ ] User state preconditions specified
- [ ] Every screen/interaction tagged with screen type (generative/static/hybrid)
- [ ] AI decision points mapped with selection criteria
- [ ] Conversational integration points defined (where AI speaks, what it says)
- [ ] Success metric defined (what constitutes journey completion)
- [ ] Abandonment handling defined (what happens if user drops off mid-journey)
- [ ] At least 2 adaptive variants described (different user contexts → different paths)
- [ ] Emotional arc documented
- [ ] Progressive trust gates identified (where new data is requested)

## Rules

- **Journeys are adaptive, not linear** — every journey must branch based on at least one user context dimension. A single linear path is a flow, not a journey.
- **AI is an active participant** — the AI doesn't just present screens; it makes decisions about what to show, when to intervene, and when to step back.
- **Journey definitions live in `product-design/journey-catalog/`** — this skill provides methodology; the actual journey definitions are product design artifacts.
- **Cross-reference components** — every journey step should reference components from `product-design/component-patterns/`. If a needed component doesn't exist, flag it for creation.

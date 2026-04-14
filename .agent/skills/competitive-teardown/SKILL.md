# Competitive Teardown — Skill Router

> **Skill type:** Analysis template  
> **Invoked by:** `/analyst`, `/research`, `/product`  
> **Last updated:** 2026-04-15

---

## Purpose

This skill provides structured templates for analyzing competing personal finance apps. Teardowns extract UX patterns, interaction design decisions, and experience gaps that inform CarrotFin's design differentiation. The focus is not on feature lists — it's on interaction design, adaptive behavior (or lack thereof), and the paradigm assumptions each competitor embeds in their UX.

## When to Use This Skill

| Scenario | Action |
|---|---|
| **Full teardown** — deep analysis of a competitor's entire UX | Use the full teardown template. Expect 2+ hours of analysis. Schedule as dedicated research. |
| **Partial teardown** — analyze a specific flow or screen | Use the screen analysis template. Focus on one interaction pattern. |
| **Quick comparison** — compare a specific feature across competitors | Use the UX gap checklist. Focus on one dimension. |
| **Market positioning update** — update competitive landscape | Skip this skill — use `/research` workflow with `market-intel.md` as context. |

## Directory Navigation

```
competitive-teardown/
├── SKILL.md              ← You are here — routing guide
├── templates/            ← Analysis templates
│   └── _index.md         ← Template inventory
└── resources/            ← Checklists and reference materials
    └── _index.md         ← Resource inventory
```

## Full Teardown Template

```markdown
# Competitive Teardown: [App Name]

> **Date:** YYYY-MM-DD  
> **Analyst:** [workflow that produced this]  
> **App version:** [version analyzed]  
> **Platform:** [iOS/Android/Web]

## Executive Summary
[2-3 sentences: what this app does, its core paradigm, and the biggest UX gap CarrotFin can exploit]

## Paradigm Analysis
| Dimension | This App | CarrotFin's Approach | Gap/Opportunity |
|---|---|---|---|
| Navigation model | [tabs/menu/search] | [emergent/AI-led] | [what's better] |
| Personalization depth | [none/segment/individual] | [individual/contextual] | [what's better] |
| AI integration | [none/chatbot/copilot/native] | [native] | [what's better] |
| Information density | [sparse/moderate/dense] | [adaptive] | [what's better] |
| Onboarding model | [form-dump/progressive/none] | [conversational progressive] | [what's better] |

## Screen-by-Screen Analysis
### [Screen Name]
- **Layout:** [description]
- **Personalization:** [what adapts, what's static]
- **Interaction pattern:** [taps, swipes, forms, conversation?]
- **What works:** [genuine strengths]
- **What's missing:** [gaps relative to CarrotFin's thesis]

## Interaction Extraction
[Key interaction patterns worth studying — both to learn from and to explicitly reject]

## UX Gaps (CarrotFin Opportunities)
| Gap | Severity | CarrotFin Response |
|---|---|---|

## Key Takeaways
[3-5 actionable insights for CarrotFin's design]
```

## Screen Analysis Template (Partial Teardown)

```markdown
# Screen Analysis: [App Name] — [Screen Name]

> **Date:** YYYY-MM-DD

## Screenshot Description
[What users see — layout, components, density]

## Interaction Pattern
[How users interact — taps, scrolls, forms, gestures]

## Personalization Assessment
[What adapts per user? What's static for everyone?]

## CarrotFin Comparison
[How would CarrotFin's adaptive paradigm handle this same user intent differently?]
```

## Rules

- **Respect competitors** — note genuine strengths, not just weaknesses. CarrotFin can learn from good design.
- **Focus on paradigm, not polish** — a competitor's color scheme is irrelevant. Their navigation model, personalization depth, and AI integration strategy matter.
- **Date everything** — app UX changes frequently. All teardowns are snapshots with `[as of YYYY-MM-DD]`.
- **Archive after 60 days** — competitive teardowns stale quickly. Move to `_archive/` after 60 days.
- **Teardowns live in `research/competitors/`** — this skill provides templates; the actual teardowns are research artifacts.

# UX Design System — Pattern Inventory

> **Last updated:** 2025-04-15

---

## Status

This pattern library is in its initial state. Patterns will be added as the `/ux-designer` workflow produces component designs during product development sessions.

## Categories

| Category | Patterns | Status |
|---|---|---|
| GenUI (generative UI) | — | Empty — awaiting first component designs |
| Adaptive Layout | — | Empty — awaiting layout rule definitions |
| Confidence Indicators | — | Empty — awaiting confidence display designs |
| Streaming UI | — | Empty — awaiting progressive rendering patterns |
| Disposable Interfaces | — | Empty — awaiting ambient layer designs |

## Pattern Template

When adding a new pattern, use this structure:

```markdown
# [Pattern Name]

> **Category:** [genui / adaptive-layout / confidence / streaming-ui / disposable]  
> **Created:** YYYY-MM-DD  
> **Used by:** [journey or screen references]

## Purpose
[What problem this pattern solves]

## User Context Inputs
[What dimensions of user state this pattern accepts]

## Adaptive Behavior Matrix
| Dimension | Low | Medium | High |
|---|---|---|---|
| Financial literacy | [behavior] | [behavior] | [behavior] |
| Risk tolerance | [behavior] | [behavior] | [behavior] |
| Trust level | [behavior] | [behavior] | [behavior] |

## Composition Rules
- Where it can appear: [surface types]
- What it can't coexist with: [conflicting patterns]
- Max instances per surface: [number]

## Conversational Integration
- AI introduction trigger: [when the AI places this component]
- Conversational exit: [what happens when the user engages with it]

## Visual Spec
[Description or reference to mockup]
```

---

*Update this index when new patterns are added. Patterns without at least one usage reference after 30 days should be reviewed for archival.*

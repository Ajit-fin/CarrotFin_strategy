# Frontend Design — Technology Inventory

> **Last updated:** 2025-04-15

---

## Status

*No technology evaluations yet. This directory will contain technology evaluation documents as architectural decisions are explored.*

## Evaluation Criteria

When evaluating frontend technologies for CarrotFin, assess against these CarrotFin-specific requirements:

| Criterion | Weight | Why |
|---|---|---|
| Dynamic composition support | High | Can the framework efficiently render AI-assembled screen compositions with variable component types and counts? |
| Adaptive rendering performance | High | Can components re-render with different adaptive behaviors without layout shifts or jank on mobile? |
| Conversational UI integration | High | Does the framework handle mixed message + rich-component streams natively or with reasonable effort? |
| Streaming/progressive rendering | Medium | Can the framework handle progressive content delivery from AI responses? |
| Cross-platform potential | Medium | Mobile-first, but web/tablet potential matters for future. |
| Developer productivity (2-person team) | High | Can two developers build and iterate quickly? |
| Community/ecosystem | Medium | Available components, libraries, and community support? |

---

*Technology evaluations are decision-log material. Major choices warrant immutable records in `decision-log/`.*

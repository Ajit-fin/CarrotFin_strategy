# BuildSpec Inventory & Agent Architecture

> **Domain:** Handoff & Architecture  
> **Last updated:** 2026-07-29  
> **Staleness threshold:** N/A (event-driven updates)

---

## Architecture Overview

CarrotFin uses a multi-agent LLM architecture. Each agent is defined as a prompt buildspec (XML), and agents share behavior through injectable modules (`<moduleRef>`). The backend orchestrates agent invocation based on user intent and journey phase.

```
User Input
  │
  ├─→ flash_conversation (Flash) ─── Conversational response, UI directives, routing
  │     └─ imports: guardrails_v1, voice_v1
  │
  ├─→ flash_extraction_v2 (Flash) ── Group-aware entity resolution, Profile Field extraction
  │     └─ imports: (standalone)
  │
  ├─→ pro_overview_planner (Pro) ─── Goal-level reasoning, EF target sizing, plan structure
  │     └─ imports: guardrails_v1, voice_v1, goals_context_v1
  │
  └─→ pro_detail_planner (Pro) ───── Step construction, UI composition, field sequencing
        └─ imports: guardrails_v1, voice_v1, goals_context_v1
```

> **Related design decisions:** [Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md) (split planner & extraction v2)

---

## Agent Buildspecs (Prompt XML Files)

| Agent | File | Model | Purpose | Last Modified |
|---|---|---|---|---|
| Companion | [flash_conversation_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/flash_conversation_v1.xml) | Flash | Conversational response, UI directives, routing & escalation | 2026-07-28 |
| Extraction | [flash_extraction_v2.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/flash_extraction_v2.xml) | Flash | Group-aware entity resolution, Profile Field extraction | 2026-07-28 |
| Overview Planner | [pro_overview_planner_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/pro_overview_planner_v1.xml) | Pro | Goal-level reasoning, EF target sizing, scenario comparison | 2026-07-28 |
| Detail Planner | [pro_detail_planner_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/pro_detail_planner_v1.xml) | Pro | Step construction, UI component assembly, field sequencing | 2026-07-28 |
| Journey Template (EF) | [journey_template_ef_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/journey_template_ef_v1.xml) | — | Journey configuration for the Emergency Fund use case | 2026-07-21 |

### Deprecated / Deleted

| Agent | File | Status | Superseded By |
|---|---|---|---|
| Extraction v1 | `flash_extraction_v1.xml` | Deleted | flash_extraction_v2.xml ([Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md)) |
| Monolithic Planner | `pro_planning_v1.xml` | Deleted | pro_overview_planner + pro_detail_planner ([Agent-Architecture.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Agent-Architecture.md)) |

---

## Shared Modules

| Module | File | Consumed By | Purpose |
|---|---|---|---|
| Guardrails | [guardrails_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/modules/guardrails_v1.xml) | flash_conversation, pro_overview_planner, pro_detail_planner | Advisory boundaries, accuracy rules, trust architecture, privacy protocol |
| Persona | [persona_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/modules/persona_v1.xml) | pro_overview_planner, pro_detail_planner | Identity, register, tone calibration, language rules (renamed from voice_v1.xml per [Interaction-Modality.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/design-decisions/Interaction-Modality.md)) |
| Goals Context | [goals_context_v1.xml](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/modules/goals_context_v1.xml) | pro_overview_planner, pro_detail_planner | Goal lifecycle logic, behavioral rules |

---

## Compiled BuildSpec Handoff Artifacts

| Spec ID | Flow Name | Status | Date | Supersedes |
|---|---|---|---|---|
| BS-001 | Emergency Fund Setup (J01) | Draft — Compilation Complete | 2026-04-17 | — |

---

## Related Strategy Artifacts

| Artifact | Relationship |
|:---|:---|
| [agent-invocation-contracts.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/agent-invocation-contracts.md) | Inter-agent wiring diagram, input/output schemas, invocation flow, error taxonomy |
| [2026-07-11-guardrails-evaluation.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/2026-07-11-guardrails-evaluation.md) | Guardrails compatibility audit — identifies open action items for module alignment |

---

*Updated by `/buildspec` workflow after each compilation. Update status manually when the dev workspace begins/completes a build.*


# CarrotFin — Company Context

> **Domain:** Company  
> **Last updated:** 2026-07-30  
> **Staleness threshold:** 30 days  
> **Related assumptions:** C1, C2, C4, GP-01  
> **Related decisions:** Data-Model, Agent-Architecture, Visual-Identity

---

## What CarrotFin Is

CarrotFin is an **AI-native household financial intelligence system** for Indian consumers. Not a robo-advisor. Not a dashboard with a chatbot bolted on. The AI doesn't assist — it *runs* the experience. The atomic unit is the **household/family** (Data-Model, GP-01), not the individual — because in India, financial decisions are inherently collective. Every recommendation, every interaction is calibrated by the AI to who the household is, what they need right now, and what they should do next.

The AI engages the user as a financial advisor would, asking questions when it needs them, explaining reasoning, and surfacing contextually relevant information and guidance. Surface architecture (how many surfaces, their roles, navigation patterns, and specific rendering mechanisms) is a design decision that evolves with user data and product maturity, not a fixed principle.

The data model uses four entity types: **Person**, **Household**, **Relationship**, and **Goal** (deferred V2) per Data-Model.

### What CarrotFin Is NOT

- **Not a robo-advisor** — robo-advisors automate portfolio allocation. CarrotFin advises across the full spectrum of personal finance: spending, saving, insurance, investing, tax, goals.
- **Not a generic dashboard** — existing Indian finance apps (ET Money, INDmoney, Kuvera) surface the same data layout to everyone. CarrotFin's AI reasons about who the user is and what they need before deciding what to surface and how to frame it.
- **Not an incremental improvement** — the ambition is a fundamentally different kind of financial relationship, not a marginally better version of existing apps.

---

## Current Stage

| Attribute | Detail |
|---|---|
| **Stage** | Pre-funding, active development |
| **Team** | 2 co-founders (1 product/strategy + 1 tech/dev) |
| **Budget** | Zero marketing budget. Cloud credits only. |
| **Revenue** | None |
| **Users** | Zero (CarrotFin). ~300 on InsurEasy MVP. ~500 on FIRE calculator. |

### Current Development State

> The product has an active multi-agent LLM architecture (Flash Companion, Flash Extraction v2, Overview Planner, Detail Planner) defined as prompt buildspecs, a formalized data model (profile-fields-schema.json), a design system (DS01), and 5 consolidated design decision documents. The current interface is **chat-primary**: the AI interacts with users conversationally and renders structured data inline as needed. The Emergency Fund journey is the first use case. See [buildspecs/_index.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/_index.md) for the full architecture.

### Existing Products (pre-CarrotFin)

1. **InsurEasy** — AI-powered insurance guidance platform (web app). MVP live with ~300 organic users [as of 2025-04]. Conversational AI for insurance comparison and policy analysis. Continues as the insurance-specific product but is not the primary development focus.

2. **FIRE Calculator** (fi.insureasy.in) — Web-based Financial Independence, Retire Early planning tool for Indian consumers. A vibe code thought experiment — not yet publicly marketed [as of 2025-04]. Proved users want holistic financial planning beyond insurance. Key UX innovations: progressive disclosure, variant analysis, sensitivity overlays. The core EF advisory concept has evolved into CarrotFin's Emergency Fund journey — the V1 entry point.

### Relationship: InsurEasy → CarrotFin

CarrotFin is a strategic expansion, not a pivot away from InsurEasy. The insight: insurance advice is one component of personal finance — and the FIRE calculator proved users want holistic financial planning, not just insurance help.

- **InsurEasy** continues as the insurance-specific brand/product (not the main development focus)
- **CarrotFin** is the new umbrella — AI-native personal finance advisor where insurance is one vertical within a holistic financial wellness platform
- **CarrotFin is built completely from the ground up** — no components carried forward from InsurEasy or the FIRE calculator. Only learnings.

---

## What Carries Forward (Learnings Only)

| Carries Forward | Does NOT Carry Forward |
|---|---|
| User behavior insights (human validation need, confidence > information) | Insurance regulatory complexity (IRDAI) |
| Growth learnings (intent channels > broadcast, SEO declining) | Policy upload mechanics |
| Product stickiness mechanisms catalog | Agent-replacement positioning |
| FIRE calculator UX patterns (progressive disclosure, variant analysis) | InsurEasy brand constraints |
| ~300 InsurEasy + ~500 FIRE calculator users as early signal | InsurEasy codebase or components |

> See [prior-learnings.md](file:///Users/kshekhaw/Documents/CarrotFin_strategy/knowledge-base/prior-learnings.md) for the full distillation.

---

## Key Constraints

1. **Zero budget** — no paid acquisition, no external design resources, no vendor tooling
2. **Two-person team** — every decision has massive opportunity cost
3. **Pre-funding** — need to demonstrate conviction and clarity to raise; no runway cushion
4. **No direct CarrotFin user data** — all user signals are from InsurEasy/FIRE calculator (adjacent but not identical)
5. **Greenfield product** — complete freedom but also complete responsibility for every architectural choice

---

## Immediate Goals

1. **Validate the group profile model** — Does the household/family model (GP-01) resonate with Indian users? Does entity resolution work conversationally?
2. **Refine the agent architecture** — Complete the guardrails evaluation action items (Persona module rename, trust terminology standardization, companion guardrails injection)
3. **Prototype the Emergency Fund journey** — End-to-end flow from first conversation through contribution plan output
4. **Build conviction artifacts** — Enough depth in product vision and market analysis to demonstrate traction

---

*This file is updated by the `/feedback` workflow after each strategic session.*

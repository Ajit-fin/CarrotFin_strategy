# V1 Product Scope — Consolidated Design Decisions

> **Consolidates:** DD01, DD02, DD03, DD05  
> **Date range:** 2026-04-16  
> **Last updated:** 2026-07-29

---

## DD01: First Journey as Onboarding

**Decision:** The user's first financial journey doubles as onboarding — data collection is justified by the journey output (a personalized result), not a standalone profiling form. A multi-path goal discovery surface shows product breadth, but only one path is active in V1. The active journey's assessment seeds the user profile for all future features.  
**Rationale:** Eliminates form fatigue by embedding profiling inside a value-delivering experience. Near-universal relevance of the first active journey ensures broad conversion.  
**Assumptions:** EF-11, C6  
**Reversal trigger:** Reverse if >30% of first-cohort users are ineligible for the active journey and the flow fails to convert them to other features.

---

## DD02: Advisory-Only Execution Model

**Decision:** V1 recommends instrument types, amounts, and allocation rationale. No specific product names, no transaction execution, no links to third-party platforms. Users execute independently on their own bank/brokerage apps.  
**Rationale:** Eliminates SEBI/RBI/AMFI compliance overhead pre-funding. Positions CarrotFin as a thinking partner, not a distributor — trust-building for private beta.  
**Assumptions:** EF-16  
**Reversal trigger:** Revisit when funded and compliance resources are available, or when an Account Aggregator integration partner is identified.

---

## DD03: V1 Scope Boundary

**Decision:** V1 covers the minimum viable advisory cycle: discovery → assessment → target setting → allocation architecture → contribution plan. Users complete V1 knowing what they need, why, where to put money (instrument-type level), and how to build toward it. Monitoring, crisis management, and active recalibration UX are deferred to V2.  
**Rationale:** Bounds engineering scope to actionable advisory output. Monitoring is hollow at first launch (no historical data); better designed with real usage patterns.  
**Assumptions:** EF-11, C4  
**Reversal trigger:** Reverse monitoring deferral if beta users explicitly request progress tracking within first 30 days — signals retention dependency.

---

## DD05: AI-Routed Journey Entry

**Decision:** Users arrive at journeys at different stages of readiness. The AI routes them to the appropriate entry point based on conversational signals and existing profile data rather than enforcing linear progression. Each phase is data-dependent, not sequence-dependent. The AI offers routing, never forces it.  
**Assumptions:** EF-11

---

## Considered & Rejected

- **Standalone onboarding + separate first journey (DD01-A):** Classic high-drop-off pattern; forces data sharing before any value delivery.
- **Full transaction execution in V1 (DD02-C):** Requires AMFI/SEBI registration and distribution licensing — not achievable pre-funding.
- **User-selected phase entry (DD05-C):** Breaks conversational paradigm; users don't think in phases — feels like a settings menu.

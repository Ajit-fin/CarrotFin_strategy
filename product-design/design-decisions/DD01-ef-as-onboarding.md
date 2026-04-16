# DD01: EF Setup as First-Launch Onboarding

> **Date:** 2026-04-16
> **Phase:** Phase 1 — Product Scoping
> **Tension resolved:** Partial T3 (Personalization vs. Privacy) — onboarding-as-assessment resolves how to gather profile data at Trust Level: New
> **Assumptions depended on:** EF-11, C5, C6
> **Source decision:** D2 in `research/market/emergency-fund-philosophy.md §10`

---

## Context

CarrotFin needs user profile data (income stability, dependents, insurance, obligations) to deliver any meaningful financial advice. The classic approach is a standalone "onboarding" flow — a multi-step form that asks about income, family, goals — followed by a separate product experience. This creates two problems: (1) onboarding feels like paperwork before value, causing drop-off; (2) the product then has to work around a separate data collection event.

The question: should the Emergency Fund setup journey double as CarrotFin's onboarding experience, or should they be separate?

---

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| **A: Separate onboarding + EF feature** | Clear separation of app setup vs. product. Onboarding can serve multiple future features simultaneously. | Classic onboarding has high drop-off rates. User has to give data before seeing any value. Two distinct flows to design and maintain. | Medium — standard practice but known conversion problem. |
| **B: EF assessment IS onboarding (chosen)** | First interaction delivers immediate, personalized value. Data collection is justified by the product output. No "dead" onboarding time — the user is already in the product. The 8-dimension assessment profiles the user completely enough to serve other features too (insurance, retirement, tax). | If user doesn't need an EF (rare: fully funded), the onboarding thesis breaks. EF must be relevant to essentially all users for this to work. | Low — almost universally relevant. 87% informal workforce + urban professionals with no fund = near-universal target. |
| **C: Adaptive first-run (AI decides which goal to start with)** | Maximum personalization — user could start with any goal. | Extreme complexity for V1. Can't build a multi-goal onboarding before any goal is well-designed. Premature generalization. | High — V1 risk. |

> **Resolution note:** The final design is a hybrid of B + a lightweight version of C's shell. The first-run experience presents a multi-path goal discovery surface ("What would you like to work on first?") — giving users a sense of product breadth — but only the EF path is active in V1. This captures B's core benefit (EF assessment as the user-profiling vehicle) while preserving architectural extensibility from C. See J01 §1B for the full first-run design.

---

## Decision

**Option B (with lightweight C shell): Multi-path first-run, EF as the only active path.**

The first-run experience presents a **goal discovery surface** — the user sees multiple financial paths ("Build my safety net," "Plan for a goal," "Grow my wealth," "Understand my finances"). Only the Emergency Fund path is active in V1; all others show a "Coming soon" state. This is not an EF-only landing — it's a product vision shell with one live path.

The EF path is chosen as the active V1 onboarding because:
1. It's relevant to virtually every Indian user — few people have a properly sized emergency fund.
2. The 8-dimension assessment profiles the user comprehensively enough (income stability, dependents, insurance, obligations, city, age, health) to seed all future features.
3. It delivers immediate, concrete value (a personalized number with attribution) that justifies the data shared.
4. The conversational assessment format sidesteps the "form fatigue" problem of traditional onboarding.

The app shell architecture must support multi-goal navigation from day one — the EF-only constraint is a V1 content decision, not an architectural one. Future goal types slot in without restructuring the first-run experience.

---

## Consequences

**Enables:**
- Immediate personalization at first launch — no cold-start problem.
- The EF assessment results seed the user profile for all future features.
- Navigation scaffold can show the product roadmap (other goals) from day one — reduces "just an EF app" perception.

**Constrains:**
- Every future "first goal" candidate for other user cohorts (e.g., a pre-retiree for whom EF may not be #1) must be handled gracefully. V1 assumes EF is universally priority #1.
- If a user already has a full EF, the onboarding "quick check" must still deliver value — don't dead-end them because they're already covered.
- The assessment design must serve dual purpose (EF sizing + general user profiling) — Phase 2 must hold this tension consciously.

---

## Reversal Trigger

Reverse if: First-cohort data shows >30% of users self-report "I already have an adequate emergency fund" and the flow doesn't convert them to explore other features. In that case, a modality-selection onboarding ("What's most important to you right now?") becomes necessary.

# DD09: Contribution Plan Deliverable — Action Card + Salary-Day Reminder

> **Date:** 2026-04-16  
> **Tension resolved:** —  
> **Open question resolved:** Q6 (Contribution plan CTA — what does the user receive?)  
> **Assumptions depended on:** C5 (conversational+visual integration feasible), C6 (users trust AI enough to act)

## Context

Phase 5 (Contribution Planning) is the V1 journey's highest-stakes moment. The user must leave the app and set up external auto-transfers. Q6 asks: what does the user receive when they "confirm the plan"? Options: step-by-step instruction card, shareable PDF, calendar reminder, or a combination.

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| **Step-by-step instruction card (in-app)** | Most actionable. Persistent — user references it anytime. Structured per their specific plan. | In-app only — not accessible outside the app. | Card sits unused if user doesn't re-open the app. |
| **Calendar reminder (commitment device)** | Fires on salary day — behaviourally optimal timing per §1.2 (Present Bias). | Insufficient alone — reminder without instructions is useless. | Dismissed with no fallback. |
| **Action Card + Reminder (combination)** | Card provides the "what." Reminder provides the "when." Together they maximise plan → action conversion. | Two features vs. one — slightly more scope. | Acceptable for the V1 journey's most critical output. |

## Decision

**Action Card (persistent in-app) + Salary-Day Reminder (opt-in commitment device).**

1. **Action Card:** A persistent, structured card with numbered step-by-step instructions for each allocation layer's external setup. Saved to the EF goal card on the home surface — accessible anytime without re-entering the conversational flow. Contains: what to set up (auto-transfer), how much (₹ per layer), when (salary day), where (instrument type per layer). Instrument-type recommendations only (DD02/D4 advisory ceiling).

2. **Salary-Day Reminder:** An opt-in recurring monthly notification on the user's salary date. Content: "Your self-EMI of ₹[X] is due. Tap to review your action steps." Links back to the Action Card. This is a commitment device (behavioral framework §1.2 — Present Bias mitigation).

3. **PDF sharing:** Deferred to V2.

## Consequences

- The Action Card becomes a persistent element on the home surface (EF goal card). Engineering must support card persistence and retrieval outside the conversational flow.
- Salary-day reminder requires push notification infrastructure. Opt-in only (§0B privacy-aware activation).
- Action Card content is generated from Phase 4 allocation + Phase 5 contribution data. Must update on plan recalibration (V2).
- The "self-EMI" label on the Action Card reinforces behavioural framing as a persistent UI element, not just a conversational metaphor.

## Reversal Trigger

- If <20% of users who confirm the plan set up auto-transfer within 7 days, the Action Card is insufficient. Consider: deep-linking to partner bank apps, in-app execution (requires DD02 reversal), or WhatsApp-based instruction delivery.

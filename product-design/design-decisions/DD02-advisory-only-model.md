# DD02: Advisory-Only Execution Model for V1

> **Date:** 2026-04-16
> **Phase:** Phase 1 — Product Scoping
> **Tension resolved:** Scope decision — explicit boundary on what CarrotFin does vs. does not do in V1
> **Assumptions depended on:** EF-16 (advisory-only eliminates regulatory risk for V1)
> **Source decision:** D4 in `research/market/emergency-fund-philosophy.md §10`

---

## Context

Does CarrotFin V1 help users actually execute the emergency fund plan — open accounts, invest in a liquid fund, set up a standing instruction — or does it advise what to do and let the user act on external platforms?

This is a material scope decision. Execution assistance requires:
- SEBI ARN (mutual fund distribution license) or AMFI registration
- RBI Payment Aggregator compliance for any money movement
- Integration with bank APIs / account aggregator (AA) framework
- Significant engineering complexity (account opening flows, payment rails)

The alternative: a purely advisory model where CarrotFin tells the user what to do (instrument type, amount, rationale) and the user executes on their own bank/brokerage app.

---

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| **A: Advisory-only (chosen)** | Zero regulatory overhead for V1. No licensing required. Engineering focused entirely on advisory quality and UX. Ship faster, learn from real users before building transactions. | User has to execute outside the app — friction between advice and action. May reduce completion rates. | Low for V1 — acceptable for MVP. |
| **B: Assisted execution via account aggregator** | Reduces action gap — advisory + action in one flow. AA framework (RBI) allows read-only account data + some facilitated transactions. | Complex compliance and AA partnership required. Adds 3–6 months to V1 timeline. Significant additional engineering. | High for V1 — pre-funding stage, not the right time. |
| **C: Full transaction execution (AMC tie-up, direct MF investment)** | Best user experience — advice to action in one tap. | Requires AMFI/SEBI registration. Distribution licensing. Significant legal review. Not achievable pre-funding. | Very high for V1. |

---

## Decision

**Option A: Advisory-only, at instrument-type level.**

In V1, CarrotFin recommends:
- *How much* to put in each liquidity layer (₹X in instant access, ₹Y in liquid MF, ₹Z in short-term FD)
- *What type of instrument* (sweep-in FD, liquid mutual fund, short-term FD / ultra-short debt fund)
- *NOT*: specific fund names, AMC names, bank product names, or links to third-party platforms

The user is expected to execute independently on their existing bank app / brokerage platform.

**The advisory output looks like:**
> "Put ₹1.0L in a sweep-in FD or high-interest savings account for instant access. Put ₹2.0L in a liquid mutual fund — most platforms let you redeem T+1, some up to ₹50K instantly. Put ₹1.5L in a short-term FD or ultra-short debt fund — accessible in 1–3 days. Don't put everything in one bank if your total FD exceeds ₹5L."

No AMC, no fund, no bank named.

---

## Consequences

**Enables:**
- V1 ships without SEBI/RBI/AMFI compliance overhead.
- Engineering focuses on advisory quality, UX, and behavioral design — not payment/transaction rails.
- The advisory-only model is honest: CarrotFin is a "thinking partner," not a distributor. This is a defensible, trust-building positioning during private beta.

**Constrains:**
- There is an action gap between "plan confirmed" and "money moved." Phase 5 of J01 must design a clear, step-by-step external execution instruction card to minimize this gap.
- Completion metric in V1 is "contribution plan confirmed" — not "money deposited." Real fund-building tracking depends on user self-reporting or future AA integration.
- Cannot track actual EF balance or growth in V1 — monitoring surface in V2 will be limited to self-reported updates unless AA integration happens.

---

## Reversal Trigger

Revisit when: Company is funded and compliance resources are available, or when a clear Account Aggregator integration partner is identified. The advisory-only model is explicitly a V1 constraint, not a product philosophy.

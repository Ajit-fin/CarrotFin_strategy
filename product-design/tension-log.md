# CarrotFin — Design Tension Log

> **Domain:** Design  
> **Last updated:** 2025-04-15  
> **Staleness threshold:** 30 days  
> **Related assumptions:** C4, C5, C6  
> **Related decisions:** —

---

## Purpose

Unresolved design tensions — genuine conflicts between opposing forces where neither side is wrong. These aren't bugs or missing decisions. They're structural ambiguities that require more data, user signal, or prototyping to resolve. Logging them prevents premature resolution and ensures we revisit them with evidence.

---

## Active Tensions

### T1: Simplicity vs. Financial Rigor

| Force A | Force B |
|---|---|
| Users want 1-tap answers and jargon-free language. A 25-year-old first-time investor doesn't want to learn about expense ratios. | Financial accuracy requires nuance, caveats, and context. Oversimplifying "switch to this fund" without explaining fees, lock-in, and tax implications is irresponsible. |

**Why unresolved:** The right balance depends on validated user segments. A novice user's "simple enough" is an expert user's "insultingly shallow." The adaptive system must calibrate per user, but we don't yet have data on where the thresholds are for each financial literacy level.

**What would resolve it:** User testing with at least 3 literacy levels, measuring comprehension AND conversion-to-action at each complexity tier.

---

### T2: Conversational vs. Visual Primacy

| Force A | Force B |
|---|---|
| Some decisions need dialogue — exploration, education, emotional processing. "Should I take this job with lower salary but stock options?" requires a back-and-forth conversation. | Some insights need charts — portfolio performance, spending trends, goal projections. Describing a spending breakdown in words is objectively worse than showing a pie chart. |

**Why unresolved:** The right balance depends on the specific user action AND the user's engagement mode (some users are naturally conversational, others are visual). We hypothesize the AI should dynamically choose, but we haven't tested whether users tolerate or resist the AI's modality choice.

**What would resolve it:** Prototype the modality handoff patterns from `interaction-model.md` and observe whether users follow the AI's lead or fight to stay in one modality.

---

### T3: Personalization vs. Privacy

| Force A | Force B |
|---|---|
| Deep personalization requires deep data access. The more the AI knows (income, spending, debts, goals, family situation), the better the advice. | Indian users are wary of sharing financial data with any app, let alone an AI. Banking data, salary, and debt are sensitive — culturally and practically. |

**Why unresolved:** Trust is progressive (design principle #5), but we don't know the trust ramp. How many value-delivering interactions does a user need before they'll share their income? Their bank statement? Their debts? The entire UX architecture depends on this ramp speed, and we have zero data.

**What would resolve it:** Longitudinal study of data-sharing behavior across at least 50 users over 30+ days. Until then, assume conservative trust ramp and design for graceful degradation when data is withheld.

---

### T4: AI Confidence vs. Transparency

| Force A | Force B |
|---|---|
| Users want confident recommendations (B2 insight: confidence > information). "I'd do X" is more compelling than "You might consider X." Hedging kills trust. | Responsible AI requires showing uncertainty. The AI isn't always right. Overconfident bad advice is worse than cautious good advice — especially for financial decisions with real consequences. |

**Why unresolved:** We need a confidence indicator design that communicates certainty levels without undermining trust. "I'm 85% confident in this recommendation" might backfire — "what about the other 15%?" The UX must convey nuance without creating anxiety.

**What would resolve it:** Test confidence indicator prototypes (severity bars, source attribution, "things I'm less sure about" sections) with users. Find the design that maintains decisiveness while being honest.

---

### T5: Speed vs. Deliberation

| Force A | Force B |
|---|---|
| Modern app UX has trained users to expect instant responses. Any loading state feels broken. The AI must respond in <2 seconds or users bounce. | Financial decisions benefit from deliberate pacing. Rushing someone through an insurance purchase or investment selection leads to regret and churn. Some friction is protective. |

**Why unresolved:** The answer is contextual — "fast information, slow decisions" — but the line between the two is blurry. Checking your portfolio balance should be instant. Committing to a 20-year SIP should have built-in pauses. We need a framework for categorizing actions by appropriate pacing.

**What would resolve it:** Categorize the top 20 user actions by decision consequence (low/medium/high) and design interaction pacing rules for each category. Test whether users accept deliberate pacing for high-stakes actions.

---

## Resolved Tensions (Archive)

*No tensions have been resolved yet. Move tensions here with a link to the design-decision record that resolved them.*

---

*Review this log before making design tradeoffs. If a new tension emerges during any session, append it here with both forces articulated.*

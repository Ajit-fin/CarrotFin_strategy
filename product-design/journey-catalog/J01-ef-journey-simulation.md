# CarrotFin Experience Simulation: Emergency Fund Journey

> **Domain:** Product Design / Journey  
> **Last updated:** 2026-05-07  
> **Status:** Companion file — pending review for merger into `J01-emergency-fund-setup.md`  
> **Related file:** `J01-emergency-fund-setup.md`, `J01-interaction-specs.md`

> **Goal:** Reimagine the human-advisor emergency fund conversation as a CarrotFin AI-led experience.
> Each turn shows what the AI **speaks** (voice) and what appears on the **mobile screen** simultaneously.
> Principles demonstrated are annotated per turn.

---

## Design Rationale: Where the AI Deviates from the Human Advisor

| Human Advisor Pattern | CarrotFin Reimagination | Why |
|---|---|---|
| Expenses gathered across 3 verbal rounds | Pre-filled editable expense card on screen; one interaction | Screen eliminates serial verbal data collection |
| Numbers stated verbally, user processes mentally | Numbers shown visually, updating live | Screen carries data; voice carries meaning |
| Band framework explained verbally | Shown as a visual thermometer the user can see and feel | A picture of 3 bands is instantly understood; 6 sentences aren't |
| "Can you cut to crisis-level spending?" asked verbally | Interactive slider — user drags their expenses down | Tactile interaction creates ownership of the number |
| ~25 conversational turns | ~9 meaningful turns | Compression without loss of warmth or depth |

---

## The Experience, Turn by Turn

---

### Turn 1 — User Opens

**🗣️ Voice:** *(silence — AI listens)*

**📱 Screen:**
Clean home surface. A single text input with mic icon. Above it, a warm greeting:
> "What's on your mind today?"

No menu. No dashboard. Just a question.

**User says:** *"I want to figure out how much emergency fund I should have."*

---

### Turn 2 — AI Acknowledges + Sets the Structure

> **Principle: P3 (Structured Thinking)** — The AI immediately provides a framework instead of a vague "sure, let's talk."

**🗣️ Voice:**
> "Good call. Let's figure this out together — I'll need to understand your situation first, then we'll land on a number that actually fits your life. Should take about 3 minutes."

**📱 Screen:**
A simple progress roadmap fades in — 4 steps, horizontally:

```
 ① Your Profile  →  ② Monthly Costs  →  ③ Risk Check  →  ④ Your Number
    ●                    ○                    ○                 ○
```

Step 1 is highlighted. The visual tells the user: *this is structured, it has a destination, and it's short.*

---

### Turn 3 — Profile Collection (Compressed)

> **Principle: P2 (Voice + Screen Optimised)** — Voice asks the warm, human question. Screen handles data capture in parallel.
> **Principle: P7 (Earn the Right to Know)** — Each field is visibly connected to the next step.

**🗣️ Voice:**
> "Tell me a bit about yourself — age, what you do, and who depends on you."

**📱 Screen:**
A clean profile card appears with labelled fields:

```
┌─────────────────────────────────┐
│  About You                      │
│                                 │
│  Age          [          ]      │
│  City         [          ]      │
│  Profession   [          ]      │
│  Household    [  Add members  ] │
│                                 │
└─────────────────────────────────┘
```

If the user speaks, fields auto-populate from voice. If they prefer tapping, they fill directly. Both paths work.

**User says:** *"I'm 35, software engineer in Bangalore. My wife, two kids, and my parents live with me."*

**📱 Screen updates live as user speaks:**
```
┌─────────────────────────────────┐
│  About You                  ✓   │
│                                 │
│  Age          35                │
│  City         Bangalore         │
│  Profession   Software Engineer │
│  Household    Wife, 2 kids,     │
│               2 parents         │
│                                 │
│  ─────────────────────────────  │
│  👥 5 dependents · Single earner│
│  → Higher coverage recommended  │
└─────────────────────────────────┘
```

The summary line at the bottom appears *immediately* — the user sees that CarrotFin understood the implication, not just the data.

---

### Turn 4 — Income + Insight

> **Principle: P6 (Read the Room)** — The AI acknowledges the weight of being a single earner without being alarmist about it.

**🗣️ Voice:**
> "Single-earner household with this many dependents — that means we should build a stronger buffer than average. What's your monthly take-home after tax?"

**📱 Screen:**
Profile card collapses upward. A single clean input appears:
```
┌─────────────────────────────────┐
│  Monthly take-home (after tax)  │
│                                 │
│     ₹ [_______________]        │
│                                 │
│  💡 After all deductions —      │
│     what hits your bank.        │
└─────────────────────────────────┘
```

**User says:** *"About 2.7 lakhs."*

---

### Turn 5 — Expenses (The Big Compression)

> **Principle: P1 (Context Over Convention)** — Smart defaults based on the profile (Bangalore, 50L income, family of 6) eliminate 3 rounds of verbal Q&A.
> **Principle: P2 (Voice + Screen)** — Voice provides the framing and permission to adjust. Screen carries the data.

**🗣️ Voice:**
> "Now let's map your essential monthly costs. I've estimated these based on your profile — just adjust anything that's off."

**📱 Screen:**
A pre-filled expense breakdown card appears. Each row has a category, a pre-filled amount, and is tappable/editable. A running total updates live at the bottom.

```
┌──────────────────────────────────────┐
│  Essential Monthly Costs             │
│                                      │
│  🏠 Home Loan / Rent    ₹ 60,000  ✎ │
│  🛒 Groceries & Home    ₹ 30,000  ✎ │
│  🎒 Kids (School/Tutor) ₹ 20,000  ✎ │
│  📱 Utilities & Transport₹ 20,000  ✎ │
│  💊 Parents' Medical     ₹ 10,000  ✎ │
│  🛡️ Insurance Premiums   ₹  8,000  ✎ │
│  👤 Domestic Help         ₹  8,000  ✎ │
│                                      │
│  ─────────────────────────────────── │
│  Total                   ₹ 1,56,000 │
│                                      │
│  [ Looks right ✓ ]                   │
└──────────────────────────────────────┘
```

**User adjusts** a few fields by tapping: Home loan → 65K, Groceries → 35K, Kids → 25K, Parents' Medical → 15K, Transport → 22K.

**📱 Total updates live to: ₹1,83,000**

User taps **"Looks right ✓"**

---

### Turn 6 — Expense Confirmed + Risk Assessment

> **Principle: P3 (Structured Thinking)** — The AI reframes the number meaningfully (survival-plus-stability) rather than just echoing it back.

**🗣️ Voice:**
> "₹1.83 lakhs a month — that's your survival-plus-stability number. Not trying to be exact. Now, one thing that really changes the answer: how stable does your job feel right now?"

**📱 Screen:**
The expense card collapses into a confirmed summary pill at the top. Below it, three tappable cards appear:

```
  Monthly essentials: ₹1.83L ✓

┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  Very Stable │ │  Reasonably  │ │    Some      │
│              │ │   Stable     │ │  Uncertainty │
│   😌         │ │   🙂         │ │   😐         │
│              │ │              │ │              │
│  6 mo buffer │ │  9 mo buffer │ │ 12 mo buffer │
└──────────────┘ └──────────────┘ └──────────────┘
```

Each card shows a subtle preview of what the stability assessment implies — the user can *see* the consequence of their answer before they tap.

**User taps:** *"Some Uncertainty"*

---

### Turn 7 — Crisis-Mode Spending (Interactive Slider)

> **Principle: P4 (Firm on Principles)** — The AI introduces the distinction between normal and crisis spending. This is a financial planning principle most users don't think about.
> **Principle: P2 (Voice + Screen)** — Voice explains the *why*. Screen makes it tactile.

**🗣️ Voice:**
> "Fair. One more thing — and this actually matters more than the full number. If your income stopped tomorrow, what could your family realistically cut down to? Drag the slider to your crisis-mode level."

**📱 Screen:**
An interactive slider appears. The user's expense categories are shown, with some flagged as "cuttable" (dining, discretionary transport, tutor extras) and some as "fixed" (EMI, school fees, medical). The slider starts at ₹1.83L.

```
┌──────────────────────────────────────┐
│  Crisis-Mode Monthly Spending        │
│                                      │
│  If income stopped, what's the       │
│  minimum your family needs?          │
│                                      │
│  ₹1.2L ━━━━━━━━━●━━━━━━━━━━ ₹1.83L  │
│              ↑                       │
│          ₹1,50,000                   │
│                                      │
│  Fixed (can't cut):                  │
│   Home EMI ₹65K · School ₹25K       │
│   Medical ₹15K · Insurance ₹8K      │
│                                      │
│  Adjustable:                         │
│   Groceries · Transport · Help       │
│                                      │
└──────────────────────────────────────┘
```

**User drags slider to ₹1.5L**

---

### Turn 8 — The Answer (Visual Reveal)

> **Principle: P3 (Structured Thinking)** — Three bands, not one number. The AI gives the user a decision framework, not a prescription.
> **Principle: P4 (Firm + Empathetic)** — The AI recommends a specific starting point, doesn't hide behind "it depends."
> **Principle: P2 (Voice + Screen)** — Voice delivers the conviction. Screen delivers the map.

**🗣️ Voice:**
> "Here's your emergency fund map. I'd start by getting to the green line — that's 9 months of crisis spending. It covers a job search without panic. Once you're there, build toward gold at your own pace."

**📱 Screen:**
A visual thermometer / band chart materializes with the user's actual numbers:

```
┌──────────────────────────────────────┐
│                                      │
│  Your Emergency Fund Map             │
│                                      │
│     ┃                                │
│  ₹18L ┃ ▓▓▓▓▓▓▓▓▓▓▓▓  Strong buffer │
│     ┃ ▓▓▓▓▓▓▓▓▓▓▓▓  (12 months)    │
│     ┃                                │
│  ₹13.5L┃ ████████████  Good target ★ │
│     ┃ ████████████  (9 months)      │
│     ┃                                │
│   ₹9L ┃ ░░░░░░░░░░░░  Minimum safe  │
│     ┃ ░░░░░░░░░░░░  (6 months)     │
│     ┃                                │
│   ₹0  ┃──────────────                │
│                                      │
│  ★ Recommended starting target       │
│                                      │
│  Based on: ₹1.5L/mo crisis spend ×   │
│  single earner × sector uncertainty  │
│                                      │
└──────────────────────────────────────┘
```

---

### Turn 9 — Reframe + Close

> **Principle: P6 (Read the Room)** — Anticipates the reaction ("that sounds like a lot") and preempts it with meaning.
> **Principle: P3 (Structured Thinking)** — Reframes the number from "money sitting idle" to "time you're buying."

**🗣️ Voice:**
> "I know ₹13 lakhs sounds like a lot sitting in a savings account. But think of it this way — you're not saving money, you're buying 9 months of time. Time to find the right job, not the first job. Time to handle a medical event without touching your investments. That's what this fund is."

**📱 Screen:**
Below the thermometer, a simple reframe card appears:

```
┌──────────────────────────────────────┐
│                                      │
│  You're not saving money.            │
│  You're buying time.                 │
│                                      │
│  ₹13.5L = 9 months to:              │
│   · Find the right job, not any job  │
│   · Handle a medical event calmly    │
│   · Keep EMIs and school fees going  │
│   · Avoid panic-selling investments  │
│                                      │
└──────────────────────────────────────┘
```

A subtle prompt at the bottom:
```
  "Want to figure out how to build this?"
  [ Yes, let's plan it ]  [ Save for later ]
```

---

## Summary: What Changed from Human to AI

| Metric | Human Advisor | CarrotFin |
|---|---|---|
| **Total turns** | ~25 | 9 |
| **Time to answer** | ~15-20 min | ~3 min |
| **Expense collection** | 3 serial verbal rounds | 1 pre-filled editable screen |
| **Number crunching** | Advisor pauses, calculates, states verbally | Live computation, visually rendered |
| **Crisis-spending question** | Verbal, user guesses | Interactive slider with fixed/cuttable breakdown |
| **Final answer delivery** | 3 numbers stated verbally | Visual band chart with recommendation highlighted |
| **"Is this too much?" anxiety** | Advisor addresses reactively | AI preempts with reframe ("buying time") |

## Principles Demonstrated Per Turn

| Turn | Primary Principle | How |
|---|---|---|
| 2 — Structure | P3 Structured Thinking | Roadmap shown immediately |
| 3 — Profile | P2 Voice + Screen, P7 Earn the Right | Voice asks warm question; screen captures data; implication shown instantly |
| 4 — Income | P6 Read the Room | Acknowledges weight of single-earner without alarm |
| 5 — Expenses | P1 Context Over Convention | Smart defaults from profile eliminate 3 rounds |
| 6 — Risk | P3 Structured Thinking | Consequence-preview cards (user sees impact before choosing) |
| 7 — Crisis slider | P4 Firm on Principles | Introduces crisis vs. normal distinction (a principle users miss) |
| 8 — The answer | P2 Voice + Screen, P4 Firm | Voice = conviction; Screen = the visual map |
| 9 — Reframe | P6 Read the Room | Preempts "that's too much" anxiety with meaning |

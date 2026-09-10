# CarrotFin — Interaction Model

> **Domain:** Design  
> **Last updated:** 2026-08-28  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C6  
> **Related decisions:** interaction-model (absorbed DD06)

---

## The Core Problem This Solves

Every existing finance app separates conversation from visualization in a way that breaks context. If a chat feature exists, it's behind a support icon — a separate surface where you ask questions and get text responses, completely disconnected from the data and insights that live elsewhere. You're constantly switching contexts with no shared state between them.

CarrotFin rejects this disconnection. Conversation and contextually relevant data presentation are not two isolated features — they're one interaction paradigm sharing context, state, and intent. A chart shown inline IS part of the conversation. A recommendation card IS the AI's advice rendered visually. **The anti-pattern:** surfaces or modes that don't share context, state, or intent — two products glued together rather than one product that flows.

---

## How CarrotFin Interacts with Users

### The Conversational Stream

The primary interaction surface. A vertical flow where the AI engages the user as a financial advisor would — asking questions, explaining reasoning, surfacing insights — and the user responds.

What makes this different from a standard chatbot:

- **Responses can contain structured visual elements.** Where words alone would be insufficient or clunky — a target calculation, a spending breakdown, an option comparison — the AI surfaces the right data and the app renders it inline, within the conversation. The user doesn't leave the stream to see a chart; the chart appears as part of the conversation.
- **User responses can be structured.** Instead of only typing, the user can tap option chips, adjust range pickers, or confirm inline — all within the conversational flow.
- **The stream is not just messages.** It's a sequence of AI-generated advisory turns woven with appropriate contextual display: text, inline inputs, data summaries, insight cards, action confirmations.

**When the stream leads:** Discovery, exploration, education, decision-making. "What should I do with my bonus?" "Am I saving enough?" "Help me understand SIPs." — any intent where the AI needs to understand context, guide reasoning, or build trust through dialogue.

### The Ambient Layer

Background intelligence that surfaces proactively without user initiation.

- **Nudges:** "You haven't reviewed your portfolio in 2 weeks. Returns are up 4%."
- **Alerts:** "Your credit card bill is due in 3 days. You have sufficient balance."
- **Opportunities:** "Tax-saving season ends March 31. You've used ₹1.08L of your ₹1.5L 80C limit."

**Delivery channels:** Push notifications or in-app banners that carry relevant context — not bare text, but prompts that include enough data to be immediately useful and naturally invite engagement.

> **Note:** Additional interaction modalities (dashboards, monitoring surfaces, reporting screens) are design decisions to be made as the product evolves and user behavior data emerges. These are not ruled out — they just aren't in the current product. When they are added, the principle that guides them is the same: match the interaction style to the user's intent and behavioral mode.

---

## Flow Modes: How Intent Maps to Interaction

The AI selects the right interaction approach based on what the user is trying to do.

| User Intent | Interaction Dynamic | Why |
|---|---|---|
| **Explore / Learn** ("What should I do?") | Progressive Dialogue | Open-ended discovery requires back-and-forth. The AI asks clarifying questions and builds understanding progressively. |
| **Decide** ("Should I increase my SIP?") | Advisory with Decision Support | Starts by understanding the question, then surfaces structured visualizations (projections, comparisons) to support the decision. |
| **Track** ("How's my spending?") | Direct Data with Interpretive Frame | The user wants state, not a long conversation. The AI surfaces the relevant summary immediately, wrapped in brief, contextual analysis. |
| **Act** ("Start a new SIP for ₹5,000") | Structured Confirmation | Task completion should be frictionless. Clear parameters, clear action, clear confirmation without unnecessary dialogue. |
| **React** (responds to an AI nudge) | Ambient → Active Transition | A background nudge triggers engagement. The AI seamlessly expands the context of the nudge into an active advisory session. |

> The specific UI patterns (e.g., chat streams, dashboards, inline forms) used to fulfill these dynamics will evolve. The core principle — matching the cognitive weight of the interface to the user's intent — remains constant.

---

## Conversational Design Principles

### The AI Has Opinions

The AI is not a search engine. It doesn't return options and ask the user to decide. It recommends, with reasoning.

- ❌ "There are several investment options available for your risk profile."
- ✅ "For your situation — 30, single, high risk tolerance, 20-year horizon — I'd put 70% in equity index funds and 30% in debt. Here's why, and here's what changes if your timeline shortens."

### One Thread at a Time

The AI never overwhelms. It focuses on one financial topic per conversational thread. If the user asks about spending while discussing investments, the AI acknowledges and returns to the original thread with an offer to switch.

### Inline Data Collection

> **Design rationale (from DD06):** Assessment is conducted stream-primary with no separate form surface during data collection. A form-like surface during assessment breaks the conversational trust ramp and cognitive flow. Inline components (option chips, range pickers) handle structured data capture within the stream.

The AI doesn't front-load questions. It asks for data when it needs it, explaining why.

- ❌ "Please enter your income, expenses, age, and goals" (form dump).
- ✅ "How old are you?" → [answer] → "And roughly, what's your monthly take-home?" → [answer] → "Got it. Here's what most people your age in your income bracket are saving. Let me show you how you compare."

Each question is immediately followed by value — the user sees why that data point mattered.

### Progressive Complexity

The conversation starts simple and reveals complexity only when the user's behavior signals readiness.

- **Session 1:** "Here are 3 things you should probably do with your money."
- **Session 5:** "Your emergency fund is solid. Ready to look at tax-saving investments?"
- **Session 20:** "Based on your portfolio, here's a rebalancing suggestion with tax implications."

---

## What "Integrated" Looks Like — Three Scenarios

### Scenario A: First-Time User

1. **AI (text):** "Welcome. Let's figure out one thing: do you have an emergency fund?"
2. **User (tap):** Selects "Not sure" from inline options
3. **AI (text + inline visual):** "Most financial advisors say 3-6 months of expenses. Here's what that looks like for you." → An inline calculator appears with a slider for monthly expenses → user adjusts → result updates live
4. **AI (text):** "Based on ₹45K/month, you'd want ₹1.35L–₹2.7L set aside. Want to set this as your first goal?"

### Scenario B: Returning User, Spending Alert

1. **Ambient nudge:** "Your spending this week is 35% above average."
2. **User taps nudge → opens conversational stream**
3. **AI (text + inline chart):** "Here's the breakdown." → Category-level spending chart, inline, with dining and shopping highlighted.
4. **AI (text):** "Dining is the big one — ₹6K in 4 days. This pace would put you ₹12K over your monthly budget. Want me to set a dining spending alert?"

### Scenario C: Experienced User, Portfolio Question

1. **User:** "How's my portfolio doing?"
2. **AI (text + inline summary):** Responds with a performance summary inline — key metrics, a brief chart — plus interpretive framing: "You're up 12%, but small-cap allocation has drifted from 15% to 11%. Rebalancing is worth looking at."
3. **User:** "Show me the rebalancing options"
4. **AI:** Surfaces an inline comparison — current vs. rebalanced trajectory — within the same stream.

---

*This file defines how CarrotFin's interactions work today. It pairs with `screen-taxonomy.md` (component vocabulary within the stream) and `ux-philosophy.md` (why these patterns exist).*

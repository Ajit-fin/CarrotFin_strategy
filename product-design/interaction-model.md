# CarrotFin — Interaction Model

> **Domain:** Design  
> **Last updated:** 2026-04-15  
> **Staleness threshold:** 90 days (foundational)  
> **Related assumptions:** C5, C6  
> **Related decisions:** —

---

## The Core Problem This Solves

Every existing finance app separates conversation from visualization. If a chat feature exists, it's behind a support icon — a separate surface where you ask questions and get text responses. The dashboard, portfolio view, and insights live elsewhere. You're constantly switching contexts.

CarrotFin rejects this separation. Conversation and visualization are not two features — they're one interaction paradigm. The AI speaks through both words AND visual elements simultaneously. A chart IS part of the conversation. A recommendation card IS the AI's advice rendered visually. The user doesn't navigate between "chat" and "dashboard." They experience one continuous, intelligent surface.

---

## Surface Types

The interaction model defines three surface types that coexist, not compete.

### 1. The Conversational Stream

The primary interaction surface. A vertical flow where the AI speaks and the user responds. But unlike traditional chat:

- **Messages can contain visual elements.** The AI doesn't say "here's a chart" and link to a separate page. The chart materializes *within* the conversation, inline.
- **User responses can be structured.** Instead of only typing, the user taps buttons, adjusts sliders, selects from options — all inline within the conversation flow.
- **The stream isn't just messages.** It's a sequence of AI-generated interface fragments: text, charts, cards, forms, comparisons, alerts, nudges — woven together with conversational connective tissue.

**When the stream leads:** Discovery, exploration, education. "What should I do with my bonus?" "Explain SIPs to me." "Am I saving enough?" — open-ended queries where the AI guides the user through a thought process.

### 2. The Composed Surface

A full-screen interface assembled by the AI from components. No conversational thread — just a contextually relevant arrangement of cards, charts, trackers, and actionable elements.

**When the composed surface leads:** Tracking, monitoring, comparison. "Show me my spending this month." "How are my goals doing?" — the user wants to see data, not have a conversation about it.

**Key distinction from a static dashboard:** composed surfaces are AI-assembled, not pre-designed. The components, their order, their density, and their content adapt to user context. Two users opening "My Goals" see different compositions based on their goals, progress, and what the AI thinks they should focus on.

### 3. The Ambient Layer

Background intelligence that surfaces proactively without user initiation.

- **Nudges:** "You haven't checked your portfolio in 2 weeks. Returns are up 4%."
- **Alerts:** "Your credit card bill is due in 3 days. You have sufficient balance."
- **Opportunities:** "Tax-saving season ends March 31. You've used ₹1.08L of your ₹1.5L 80C limit."

**Delivery channels:** Push notifications, in-app banners, or conversation-starter cards on the home surface. Each has attached visual context — not a bare text notification, but a notification with a relevant mini-visualization.

---

## Flow Modes: When Each Modality Leads

The AI dynamically selects the right modality based on intent and context.

| User Intent | Modality | Why |
|---|---|---|
| **Explore / Learn** ("What should I do?") | Conversational Stream | Open-ended discovery needs dialogue. The AI asks clarifying questions, responds to reactions, builds understanding progressively. |
| **Decide** ("Should I increase my SIP?") | Stream → Visual pivot | Starts conversational (understanding the question), pivots to visualization (showing projections, comparisons) for decision support. |
| **Track** ("How's my spending?") | Composed Surface | The user wants data, not a conversation. Show the answer directly with charts and metrics. |
| **Act** ("Start a new SIP for ₹5,000") | Composed Surface with inline confirmation | Task completion doesn't need a conversation. Clear form, clear action, clear confirmation. |
| **React** (responds to an AI nudge) | Ambient → Stream transition | A nudge (ambient) triggers the user to engage. The AI expands the nudge into a conversational or visual thread. |

### Modality Handoffs

The most critical design challenge: transitioning between conversational and visual modes without jarring the user.

**Smooth handoffs look like this:**
1. User asks "Am I spending too much on dining?"
2. AI responds with a text insight: "You spent ₹14K on dining this month — that's 2× your 3-month average."
3. *Below* the text, a spending breakdown chart materializes with dining highlighted.
4. Below the chart, the AI asks: "Want me to suggest a monthly dining budget that keeps your savings on track?"

**Bad handoffs:** "Click here to see your spending dashboard." Any handoff that sends the user to a different page breaks the integrated paradigm.

---

## Conversational Design Principles

### The AI Has Opinions

The AI is not a search engine. It doesn't return options and ask the user to decide. It recommends, with reasoning.

- ❌ "There are several investment options available for your risk profile."
- ✅ "For your situation — 30, single, high risk tolerance, 20-year horizon — I'd put 70% in equity index funds and 30% in debt. Here's why, and here's what changes if your timeline shortens."

### One Thread at a Time

The AI never overwhelms. It focuses on one financial topic per conversational thread. If the user asks about spending while discussing investments, the AI acknowledges and returns to the original thread with an offer to switch.

### Inline Data Collection

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

## What "Woven" Looks Like — Three Scenarios

### Scenario A: First-Time User

1. **AI (text):** "Welcome. Let's figure out one thing: do you have an emergency fund?"
2. **User (tap):** Selects "Not sure" from inline options
3. **AI (text + visual):** "Most financial advisors say 3-6 months of expenses. Let me show you what that looks like." → An inline calculator appears with a slider for monthly expenses → user adjusts → result updates live
4. **AI (text):** "Based on ₹45K/month, you'd want ₹1.35L–₹2.7L set aside. Want to set this as your first goal?"

### Scenario B: Returning User, Spending Alert

1. **Ambient nudge:** "Your spending this week is 35% above average."
2. **User taps nudge → opens conversational stream**
3. **AI (text + chart):** "Here's the breakdown." → Category-level spending chart, inline, with dining and shopping highlighted.
4. **AI (text):** "Dining is the big one — ₹6K in 4 days. This pace would put you ₹12K over your monthly budget. Want me to set a dining spending alert?"

### Scenario C: Experienced User, Portfolio Review

1. **User:** "Show me how my portfolio is doing"
2. **Composed surface renders:** Asset allocation donut, performance chart, benchmark comparison — all on one surface, no conversation needed.
3. **AI annotation (text overlay on chart):** "Your small-cap allocation dropped from 15% to 11% due to market movement. Rebalancing would bring it back — should I show you how?"
4. **User taps "Show me"** → inline projection appears comparing rebalanced vs. current trajectory.

---

*This file defines how CarrotFin's interactions work. It pairs with `screen-taxonomy.md` (which screens use which patterns) and `ux-philosophy.md` (why these patterns exist).*

# CarrotFin — High Level Product UX Requirements

> **Purpose:** Context document for external brainstorming  
> **Date:** 2026-04-24  
> **Audience:** External system / collaborator needing product UX context

---

## Core UX Thesis

The UX is not a better dashboard or a chatbot with charts. It's a new category of financial interface where the AI is the architect of the experience — assembling a unique interface for each user based on who they are, what they need, and what they should do next. The closest analogy: a skilled human financial advisor who adjusts depth, vocabulary, and focus per person — digitally, at scale.

---

## Interface Architecture

### Three Surface Types

1. **Conversational Stream** — Primary surface. Vertical flow where AI speaks and user responds. Messages contain visual elements inline (charts, sliders, cards, forms). User responses can be structured (tap buttons, adjust sliders) — not just text.

2. **Composed Surface** — Full-screen AI-assembled layouts from component palette. For tracking, monitoring, comparison. No conversation thread — just contextually relevant data. Two users viewing "My Goals" see different compositions.

3. **Ambient Layer** — Background proactive intelligence: nudges, alerts, opportunities. Delivered via push, in-app banners, or conversation-starter cards. Each notification carries attached visual context.

### Screen Types

- **Generative** — AI composes at runtime. This is where differentiation lives. Default bias.
- **Static** — Fixed layout, same for all. For legal, regulatory, security, settings.
- **Hybrid** — Fixed skeleton, AI-generated content. Structural predictability + personalized content.

---

## Interaction Requirements

### Voice + Screen Integration

- Voice is the primary input modality; screen provides synchronized visual feedback
- Voice Agent handles natural Indian English, Hinglish expressions, approximate numbers ("about eighty thousand"), and code-switching
- Visual elements materialize in sync with voice narration — charts appear as the advisor references them
- Client-side VAD for barge-in; manual interruption handling for precise control
- Privacy-sensitive data confirmed on screen, not echoed verbally

### Modality Selection

| User Intent | Modality |
|---|---|
| Explore / Learn | Conversational Stream |
| Decide | Stream → Visual pivot (projections, comparisons) |
| Track | Composed Surface (data-first) |
| Act | Composed Surface with inline confirmation |
| React to nudge | Ambient → Stream transition |

### Conversational Design

- **AI has opinions** — recommends with reasoning, doesn't list options
- **One thread at a time** — focused, never overwhelming
- **Inline data collection** — asks when needed, explains why, immediately shows value
- **Progressive complexity** — starts simple, reveals depth as user signals readiness

---

## User State Model (Personalization Inputs)

| Dimension | Adapts |
|---|---|
| Financial literacy (1-5) | Explanation depth, jargon, component density |
| Risk tolerance | Recommendation aggressiveness, chart framing |
| Life stage | Topic prioritization, default goal suggestions |
| Engagement mode (conversational / visual / hybrid) | Interaction modality balance |
| Emotional state (calm / anxious / excited / overwhelmed) | Tone, pacing, information density |
| Trust level (New / Warming / Trusting / Dependent) | Data requests, recommendation confidence, automation rights |

Day-one: most dimensions unknown. AI must deliver value with minimal context and deepen personalization as it learns.

---

## Behavioral Intelligence Requirements

### What the AI Adapts (Presentation)

- Vocabulary (plain Hinglish ↔ financial terminology)
- Explanation depth (headline only ↔ full analysis)
- Framing (loss ↔ gain ↔ neutral — chosen deliberately per behavioral objective)
- Emotional tone (reassuring ↔ direct ↔ urgent)
- Information density and pacing

### What the AI Cannot Compromise (Substance)

- Mathematical accuracy — numbers checked, not approximated
- Material risk disclosure — simplify language, never omit facts
- Honest uncertainty — state assumptions, qualify projections
- Regulatory compliance — explained simply, never skipped

### Trust Architecture

- Progressive: value delivered unlocks data requests and behavioral authority
- **Value-before-ask** — hard constraint. Every data request preceded by demonstrated value.
- Trust gates AI capabilities: passive insights (New) → active recommendations (Warming) → friction interventions (Trusting) → automated actions (Trusting+)
- Trust can regress (bad advice outcome, security concerns, inactivity)

### Verification Anchors ("Show Your Work")

- Every AI-generated number traceable to inputs, formulas, data sources
- Progressive disclosure: headline → reasoning → formula → data sources
- Proactively shown for first-time projections, counterintuitive results, consequential decisions

---

## Target User

Indian millennials/GenZ (25-35), salaried professionals in metros/Tier-1 cities. Earning ₹5L-25L annually — need planning but can't access human advisors. Comfortable with AI, underserved by static dashboard apps. At life-stage inflection points where early decisions compound massively.

---

## Key UX Differentiators vs. Category

| Dimension | Category Norm | CarrotFin |
|---|---|---|
| Home screen | Fixed layout, same for all | AI-composed, different per user |
| Navigation | Bottom tab bar | Emergent, AI-guided |
| Onboarding | 8-12 screen wizard | Conversational, progressive profiling over time |
| Recommendations | Generic ("Top SIPs this month") | Contextual, personalized, actionable |
| Insights | Passive ("Portfolio up 12%") | Active + prescriptive ("Over-allocated, move ₹50K to mid-cap") |
| Voice | Separate chat tab or absent | Integrated — conversation IS the interface |

---

## Open UX Questions

- Does adaptive UI actually outperform static dashboards? (Highest-risk assumption, untested)
- How complex can compositions get before users feel lost?
- How quickly can AI build an accurate user state model?
- Can users override AI composition? (Flexibility vs. coherence tension)
- How does Indian cultural context (joint family, community validation) affect trust architecture?

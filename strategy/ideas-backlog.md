# Ideas Backlog

> **Purpose:** Parking lot for ideas to revisit post-v1. Not prioritized, not committed — just captured.  
> **Status:** Living document  
> **Last updated:** 2026-04-25

---

## How to Use This File

- **Add freely.** One-liner + enough context to reconstruct the idea later.
- **Don't deep-dive here.** This is capture-only; analysis goes into dedicated research/design files when picked up.
- **Tag themes** to enable grouping as the list grows.
- **When picking up an idea**, move it to the appropriate workspace location (research/, product-design/, etc.) and link back here.

---

## Ideas

### Adaptive Intelligence — User Modeling

> Theme: `user-modeling` · `personalization`

| # | Idea | Context / Why It Matters |
|---|------|--------------------------|
| 1 | **Adapt to user's approach to finance** — not just scenarios | Beyond adapting screens to life situations, model the user's financial mindset (conservative vs. aggressive, planner vs. impulsive, goal-oriented vs. anxiety-driven). Advisory tone, product sequencing, and nudge frequency should shift based on financial personality, not just financial facts. |
| 2 | **Decipher Indian English & Hinglish patterns** | Indian users code-switch heavily. Voice and text layers need to handle Hinglish ("mera SIP kitna hona chahiye?"), regional English idioms, and culturally specific financial vocabulary (e.g., "FD", "chit fund", "PPF"). This is deeper than translation — it's intent extraction through cultural-linguistic context. |

---

### System Learning & Self-Improvement

> Theme: `meta-learning` · `system-evolution`

| # | Idea | Context / Why It Matters |
|---|------|--------------------------|
| 3 | **Continuous learning loop for internal systems** | Build mechanisms for voice layer, brain layer, and prompt systems to learn from production interactions. Track prompt drift, entity extraction accuracy, and conversation dead-ends. Feed insights back into prompt tuning and system calibration without manual intervention. |
| 4 | **Philosophy stress-testing from user interactions** | Mine real user conversations to find where our design philosophy breaks — where adaptive UI confuses instead of clarifies, where the advisor tone feels patronizing or cold, where the "no rigid formula" approach leaves users wanting a simple number. Systematic feedback loop from usage patterns to philosophy refinement. |

---

### Conversation UX — Input & Plan Interaction

> Theme: `conversation-ux` · `input-design` · `plan-approval`

| # | Idea | Context / Why It Matters |
|---|------|--------------------------|
| 5 | **Plan view with user approval and edit flow** | After the advisor generates a recommendation/plan, user needs a way to review it holistically, request edits (tweak a number, challenge an assumption), approve it, and trigger calculation or implementation — all within the same conversational context. Bridges the gap between advisory output and actionable execution. UX and system contracts both need defining. |
| 6 | **Logical input grouping with mid-flow mini-outputs** | When collecting a long set of inputs (e.g., onboarding, goal setup), the brain should cluster fields into logical semantic groups (e.g., "income picture", "existing commitments", "risk appetite") rather than a flat sequential form. After each group, surface a mini insight or partial result to sustain engagement and signal that the system is already working on the problem — not just collecting data. Reduces drop-off and builds trust incrementally. |

---

### Scenario & What-If Engine

> Theme: `what-if` · `scenario-brainstorming` · `ux` · `system`

| # | Idea | Context / Why It Matters |
|---|------|--------------------------|
| 7 | **Improve what-if / scenario brainstorming** (UX + system) | Currently ad-hoc. Needs both a UX pattern (how does the user invoke, navigate, and compare scenarios?) and a system mechanism (how does the brain hold multiple scenario branches in state, diff outputs, and present comparisons clearly?). Consider: proactive scenario surfacing ("what if you lost your job for 3 months?"), side-by-side visual comparison, and scenario save/recall. |

---

### Data, Feedback & On-Edge SLM Training

> Theme: `feedback` · `data-flywheel` · `slm-training` · `ml-ops`

| # | Idea | Context / Why It Matters |
|---|------|--------------------------|
| 8 | **User feedback capture mechanism** | How do we capture feedback without being annoying? Options: implicit (dwell time, re-asks, correction patterns, feature abandonment), explicit (thumbs, free text prompts at natural exit points), and hybrid (agent-prompted check-ins after plan milestones). Need to design the signal taxonomy before building instrumentation. |
| 9 | **Data strategy for SLM fine-tuning (on-edge deployment)** | Define what data to collect, at what cadence, and at what level of directness/indirectness to eventually fine-tune a small on-edge model for low-complexity response generation and intent routing. Candidate signals: entity extraction correctness, intent routing decisions, conversation recovery events, user correction of AI outputs, explicit ratings. Volume, labeling strategy, and privacy constraints all need scoping before this becomes tractable. |

---

## Considered & Rejected

_None yet._

# DD05: Multi-Stage Re-Entry — AI-Routed Journey Entry

> **Date:** 2026-04-16  
> **Tension resolved:** —  
> **Assumptions depended on:** C5 (conversational + visual integration), EF-11 (8-dimension sizing model)

## Context

J01's Phase 1 journey design (§2) assumes a linear flow: Discovery → Assessment → Target Setting → Fund Architecture → Contribution Planning. But the founding team identified a critical UX gap: **users arrive at the EF journey at different stages of readiness.** Some are starting from zero, some have existing savings they want validated, some have done external research and already know their target, and some are returning after abandoning a prior session.

The question: **Should the EF flow enforce a linear progression through all phases, or should the AI dynamically route users to the appropriate entry point based on their existing context?**

## Options Evaluated

| Option | Pros | Cons | Risk |
|:---|:---|:---|:---|
| **A. Always start from Phase 1** | Simplest to build. Consistent experience. Every user gets full education. | Disrespects the user's existing knowledge. Forces redundant steps. Users who already have an EF and want validation are forced through a "Do you need one?" pitch — patronizing. | Medium — attrition risk from frustrated intermediate/advanced users |
| **B. AI-driven routing based on conversational signals + app state** | Respects user autonomy. Plugs in where the user is. Natural for conversational AI. Graceful for session resumption. | Complex — each phase must be self-sufficient (not dependent on prior phase *completion*, only on prior phase *data*). AI routing logic must be robust. | Medium — incorrect routing (e.g., skipping Assessment for a user who actually needs it) could lead to poor target quality |
| **C. User explicitly selects which phase to enter** | Full user control. No AI routing errors. | Breaks the conversational paradigm — feels like a settings menu, not a conversation. Users may not know which "phase" they need. | Low risk, low engagement — users don't think in phases, they think in questions |

## Decision

**Option B: AI-driven routing based on conversational signals and serialized app state.**

The AI detects the user's EF readiness through two mechanisms:
1. **App state (returning users):** Serialized per-beat journey state from prior sessions. The AI knows exactly which phases and beats are complete and which data points are captured.
2. **Conversational probing (new users with prior knowledge):** The AI's opening question and follow-ups surface signals about the user's existing knowledge, allowing dynamic routing.

Five entry scenarios are defined (see J01 §0B):
- **A: Fresh user** → Full flow from Phase 1
- **B: Has existing savings** → Skip Discovery, enter Assessment in "validation" framing
- **C: Has a known target** → Offer validation check; if declined, skip to Fund Architecture
- **D: Returning mid-flow** → Resume from saved checkpoint with state summary
- **E: Has existing EF, wants review** → Skip to Assessment with pre-filled existing corpus data

**Key principles:**
- The AI offers, never forces. For Scenarios B and C, the AI offers to validate but accepts the user's preference to skip.
- Each phase is data-dependent, not sequence-dependent. Phase 3 (Target Setting) needs the 8-dimension data (from Phase 2), not Phase 2's completion status. If the data is available, Phase 3 can execute.
- User autonomy is paramount — see DD02 (advisory-only model) for the broader principle.

## Consequences

**Enables:**
- Users with external research (other apps, manual calculation) can plug into CarrotFin at their current stage
- Session abandonment recovery — the flow picks up exactly where it left off
- "EF health check" use case — users with existing funds get value without redundant setup
- Future-proofing: when V2 adds monitoring/recalibration, re-entry into the Assessment phase for recalibration is already architected

**Constrains:**
- Journey state must be serializable per-beat — engineering must implement a state machine that supports arbitrary entry and checkpoint resume
- Each phase's interaction spec (Steps 2B–2D) must define its minimum data prerequisites — what inputs it needs regardless of how the user arrived
- Phase 1 (Discovery) must be skippable — its value (urgency establishment, myth busting) must be available on-demand later (e.g., as contextual cards during Assessment) for users who bypassed it
- AI routing errors are a risk — if the AI misjudges a user's readiness and skips Assessment for someone who actually needs it, the target quality degrades. Mitigation: the AI always offers validation, never silently skips.

## Reversal Trigger

- User testing shows AI routing is consistently wrong (>20% of users routed to the wrong entry point need to backtrack)
- Engineering complexity of per-beat state serialization is prohibitive for V1
- Users express preference for a predictable, fixed flow ("I wanted to see the whole thing, why did it skip?") — would suggest at least offering the full flow as an option

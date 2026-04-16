# DD04: Voice + Screen Hybrid Modality

> **Date:** 2026-04-16  
> **Tension resolved:** Adjacent to T2 (Conversational vs. Visual Primacy — adds voice as a third modality axis)  
> **Assumptions depended on:** C5 (conversational + visual integration feasible)

## Context

CarrotFin's interaction model (`interaction-model.md`) defines three surface types — conversational stream, composed surface, ambient layer — all of which assume **text + visual** input/output. The EF journey (J01) is the first concrete flow to be designed, and the founding team's product vision requires voice as a first-class modality on mobile.

The question: **How does voice integrate with the existing screen-based interaction model?** Is voice a separate mode, a replacement for text, or a parallel layer? And critically: how are voice recognition errors handled for financial data where a misheard "80 thousand" vs. "8 lakh" has 10× downstream impact?

## Options Evaluated

| Option | Pros | Cons | Risk |
|:---|:---|:---|:---|
| **A. Voice-primary, screen-secondary** | Natural conversational feel. Closest to "talking to an advisor." | Screen becomes a passive display — wastes mobile's rich interaction surface. Inaccessible in noisy/public environments. Heavy ASR dependency. | High — voice recognition errors in financial context are unacceptable without screen verification |
| **B. Voice as supplementary input only** | Low risk — voice just replaces typing. Screen UX is unchanged. | Misses the opportunity for AI narration during visual reveals. Doesn't feel like a conversation — feels like speech-to-text. | Low risk, low differentiation |
| **C. Voice + Screen hybrid — screen as source of truth** | Best of both: voice for conversational naturalness, screen for persistence/verification. AI narrates while screen renders richer content. Graceful degradation to text-only. | More complex to design — every interaction must work in both modes. Voice+screen sync adds engineering complexity. | Moderate — complexity manageable if voice is an I/O layer, not a branching path |

## Decision

**Option C: Voice + screen hybrid, with screen as the persistent source of truth.**

Voice is a first-class input/output channel that **overlays** the existing conversational stream — not a separate mode. The key architectural principles:

1. **Screen-first, voice-layered.** Every interaction is designed for text+tap as the baseline. Voice affordances are added on top. No interaction requires voice.
2. **Screen is source of truth.** Voice is ephemeral; the screen persists. After any voice input, the captured value appears on screen. The user verifies via screen.
3. **Simultaneous, not sequential.** AI narrates while screen renders in parallel. The AI's spoken message is a summary; the screen always contains equal or richer information.
4. **Privacy-aware activation.** Voice is user-activated (opt-in toggle). Never defaults to on.
5. **Graduated confirmation.** Categorical voice inputs (city, age bracket) get light confirmation. Numeric voice inputs (income, obligations) get active on-screen confirmation before the flow proceeds. Phase-gate transitions get full summary review regardless of modality.

**Voice-off mode** is not a degraded experience — it's the **design baseline**. The flow is identical in content, structure, and capability. Voice adds convenience and conversational warmth; it never gates functionality.

## Consequences

**Enables:**
- Natural conversational feel for the assessment conversation (Beats 1–3)
- AI narration during visual reveals (Target Setting, Fund Architecture) — the AI "talks the user through" the chart
- Hands-free interaction for scenarios where the user is multitasking
- Inclusive design — voice input for users who are less comfortable typing

**Constrains:**
- Every interaction must be designed twice: voice+screen AND text+tap. No voice-only features.
- Numeric data requires mandatory on-screen confirmation before downstream use — adds a confirmation beat to the flow
- AI must never verbally echo sensitive data (health conditions, debts) — screen-only confirmation for sensitive inputs
- Engineering must treat voice as an I/O layer, not a conversation branching mechanism — the state machine is modality-agnostic

**New sub-tensions introduced:**
- Voice recognition accuracy in Indian English + vernacular financial terminology (e.g., "lakhs," "EMI," regional pronunciation) — needs ASR quality validation
- Privacy in shared environments — user may start in voice mode and need to switch mid-conversation. Modality switching must be seamless and state-preserving.

## Reversal Trigger

- User testing reveals that voice adds cognitive overhead rather than reducing friction (users spend more time correcting voice errors than they would have spent typing)
- ASR quality for Indian English financial terminology falls below 90% accuracy threshold
- Users overwhelmingly prefer text-only mode (>80% voice-off) — would simplify design by dropping voice as a first-class modality

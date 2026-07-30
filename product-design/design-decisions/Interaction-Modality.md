# Interaction Modality — Consolidated Design Decisions

> **Consolidates:** DD04, DD16  
> **Date range:** 2026-04-16 to 2026-07-11  
> **Last updated:** 2026-07-29

---

## DD16: Text-Only MVP (Active)

**Decision:** V1 is text-only. The voice module (`voice_v1.xml`) is renamed to persona module (`persona_v1.xml`), with emotional state triggers remapped from audio pacing to text layout instructions (paragraph length, whitespace density, bullet usage). All persona rules translate to visual text formatting.  
**Rationale:** Eliminates dead audio instructions that waste tokens and confuse text layout parsing. Re-adding voice rules later is straightforward — future `voice_v2.xml` extends persona, doesn't replace it.  
**Assumptions:** (none — this is a scope constraint, not a user behavior assumption)  
**Reversal trigger:** Voice/audio becomes a V1 requirement, or text-only persona module proves insufficient without audio-centric emotional calibration.

---

## DD04: Voice + Screen Hybrid Architecture (Deferred)

> **V1 applicability:** Deferred. V1 is text-only per DD16. This decision governs future voice integration architecture. Agents should NOT read this section for V1 work.

**Decision:** Voice overlays the existing conversational stream as a first-class I/O channel, not a separate mode. Five architectural principles:
1. **Screen-first, voice-layered** — every interaction works text+tap; voice is additive
2. **Screen as source of truth** — voice is ephemeral; captured values appear on screen for verification
3. **Simultaneous, not sequential** — AI narrates while screen renders richer content in parallel
4. **Privacy-aware activation** — voice is user-activated, never defaults to on
5. **Graduated confirmation** — categorical inputs get light confirmation; numeric inputs require active on-screen verification

**Assumptions:** (none — deferred architecture decision)  
**Reversal trigger:** User testing shows voice adds cognitive overhead (correction time > typing time), or >80% of users prefer text-only.

---

## Considered & Rejected

- **Voice-primary, screen-secondary (DD04-A):** Wastes mobile's rich interaction surface; inaccessible in noisy environments; ASR errors on financial data are unacceptable without screen verification.
- **Voice as supplementary input only (DD04-B):** Low differentiation — feels like speech-to-text, misses AI narration opportunity during visual reveals.

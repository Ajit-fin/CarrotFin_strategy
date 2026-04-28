# Voice / TTS Architecture Options — CarrotFin

> **Domain:** Technology Research
> **Date:** 2026-04-18
> **Staleness threshold:** 90 days (fast-moving landscape)
> **Related:** Agent Invocation Contracts §2.6 (Composition Output), §5.6 (Knowledge Grounding), Open Question #8
> **Upstream:** DD04 (Voice+Screen Hybrid Modality), BS-001 §1.3 (Modality Rules)

---

## The Question

CarrotFin's voice layer (DD04) requires the AI to *speak* while the screen *renders* — simultaneously, not sequentially. The Agent Invocation Contracts (v0.2.0) define `visual_narrations` in the Composition agent's output, and `voice_output` in the Conversational agent's output. But the contracts don't specify **how text becomes speech**.

This document explores five architectural scenarios for the speech synthesis layer, evaluated against CarrotFin's specific constraints.

---

## CarrotFin-Specific Requirements

Before evaluating scenarios, the requirements that eliminate generic "just use Cloud TTS" advice:

| Requirement | Source | Why It's Non-Negotiable |
|:---|:---|:---|
| **Simultaneous voice + visual delivery** | DD04 Rule 3, BS-001 §1.3 | The AI's spoken message and visual render MUST start at the same time. Voice can't wait for full text generation. |
| **Two narration streams per turn** | Agent Contracts §2.2, §2.6 | Conversational response (editorial) + visual walkthrough narration (structural). Both may need TTS in the same turn. |
| **Indian English + Hindi** | User segments S1, S2 | Primary users are urbane Indian professionals. Natural Indian English accent is table stakes. Hindi support is a V2 need. |
| **Financial terminology accuracy** | BS-001 §4 | "Lakhs," "crores," "SIP," "EMI," "DICGC," "FD" must be pronounced correctly. Generic English TTS mangles these. |
| **Sub-800ms total turn latency** | Industry benchmark for financial AI | The full pipeline (STT → reasoning → TTS → render) must complete within the conversational latency window. TTS contributes ~100–200ms of this budget. |
| **Privacy: sensitive data never spoken** | DD04, BS-001 §3.2.4 | D8 (health) dimensions are screen-only. TTS must be suppressible per-field. |
| **Zero budget at launch** | Company context | No paid TTS API volume at launch. Free tier or on-device. |
| **Voice-off is the baseline** | DD04 Rule 1 | Voice is opt-in. Text+tap mode must work perfectly without any TTS infrastructure. |

---

## Scenario Analysis

### Scenario A: Cloud-Only — Gemini 3.1 Flash TTS (Dedicated TTS Model)

**Architecture:**
```
Agent Pipeline (Reasoning) → text output → Gemini Flash TTS API → audio stream → device speaker
                                                                 ↓
                                                          visual render (parallel)
```

**How it works:** The agent pipeline produces text (conversational response + visual narrations). A separate API call to Gemini Flash TTS converts each text block to audio. Audio streams to the device while visual components render in parallel.

| Dimension | Assessment |
|:---|:---|
| **Voice quality** | Excellent. 200+ audio tags for style/pacing control. Multi-speaker support. Expressive. [as of 2026-04] |
| **Indian English** | Supported. Accent quality is good for standard Indian English. Financial terminology needs validation. |
| **Latency** | 200–500ms per TTS call (network-dependent). For two narration streams in one turn, this may serialize to 400–1000ms — problematic for the latency budget. Streaming TTS (TTFB ~100ms) mitigates partially. |
| **Simultaneous delivery** | Achievable with streaming: start audio playback from partial chunks while visual renders. Requires WebSocket or streaming HTTP. |
| **Cost** | Free tier: generous for development. At scale: per-character pricing applies. Zero-user phase: essentially free. |
| **Offline** | None. Voice degrades to text-only without connectivity. Acceptable per DD04 (voice-off is baseline). |
| **Privacy** | Text leaves the device for TTS synthesis. Financial data in narration text goes to Google's servers. |
| **Complexity** | Low. Standard API integration. No on-device model management. |

**Verdict:** Strong default for V1. Streaming TTS keeps latency acceptable. Free tier covers the pre-scale period. Privacy tradeoff is real but manageable — the narration text (not raw financial data) is what goes to TTS, and the conversational response already went through Gemini for reasoning.

---

### Scenario B: Unified — Gemini Live API (Reasoning + Native Audio in One Call)

**Architecture:**
```
User utterance (audio) → Gemini Live API → native audio response (reasoning + speech in one model pass)
                                         ↓
                                    visual render instructions streamed in parallel
```

**How it works:** Instead of the current 4-agent pipeline producing text that then gets converted to speech, the Conversational agent IS the Live API — it takes audio in and produces audio + text out in a single model call. The model reasons and speaks natively.

| Dimension | Assessment |
|:---|:---|
| **Voice quality** | High. Native audio understands tone, pacing, emotion from the input and mirrors it in the output. Feels like talking to a person. |
| **Indian English** | Supported within the Live API. Accent adaptation based on user's input audio. Financial term pronunciation depends on model training. |
| **Latency** | Lowest possible. Eliminates STT→text→TTS pipeline entirely. The model speaks as it reasons. Sub-300ms perceived latency achievable. |
| **Simultaneous delivery** | Native strength — the model is designed for real-time, bidirectional audio. Visual components can trigger off the audio stream. |
| **Cost** | Higher per-turn than text-based reasoning + separate TTS. The Live API processes audio natively, which is compute-intensive. |
| **Offline** | None. Fully cloud-dependent. |
| **Privacy** | Raw audio (user's voice) goes to Google. More privacy-sensitive than text-only communication. |
| **Complexity** | High. Fundamental rearchitecture of the agent pipeline. The current 4-agent contract model assumes text-based inter-agent communication. Live API collapses Conversational + TTS into one unified streaming call. The Composition agent's `visual_narrations` field becomes redundant — the model produces speech directly. |

**The critical tension with CarrotFin's architecture:**

The Live API is optimized for free-flowing conversation. But CarrotFin's interaction model is *not* free-flowing conversation — it's a structured journey with phase gates, confirmation patterns, and multi-agent coordination. The Live API's strength (unified reasoning + speech) is also its weakness for CarrotFin: it bypasses the carefully designed inter-agent contracts.

Specifically:
1. **The Computation agent can't be "inside" the Live API.** Deterministic math must be a separate, verifiable function call.
2. **The Composition agent's rendering decisions** require reasoning over adaptive behaviour tables — this is a separate concern from conversational speech generation.
3. **The State agent** needs structured dimension updates, not raw audio tokens.
4. **Guardrail enforcement** is harder when the model is streaming audio — you can't block and re-invoke mid-speech.

**Verdict:** Architecturally incompatible with the current 4-agent design as a wholesale replacement. But potentially powerful as a *modality layer* — use the Live API for the Conversational agent's voice I/O only, while keeping the other agents text-based. This is Scenario D.

---

### Scenario C: Edge-Only — On-Device SLM for TTS

**Architecture:**
```
Agent Pipeline (cloud reasoning) → text output → on-device TTS model → audio → speaker
                                                                       ↓
                                                                visual render (parallel)
```

**How it works:** The agent pipeline runs in the cloud as designed. The resulting text is sent to the Flutter app, which runs a local TTS model (e.g., Kokoro-82M, quantized Sesame CSM, or Gemma 3n with audio) to synthesize speech on-device.

| Dimension | Assessment |
|:---|:---|
| **Voice quality** | Good and improving rapidly. MOS gap with cloud TTS has closed to ~0.1–0.2 as of 2026. Kokoro-82M achieves near-commercial quality. But expressiveness (emotion, pacing variation, emphasis) is still noticeably behind top cloud models. |
| **Indian English** | Weak spot. Kokoro has Hindi support but Indian English accent quality is not validated. Financial terminology ("lakhs," "SIP," "DICGC") will likely need custom phoneme mappings. |
| **Latency** | Excellent. 150–350ms, consistent, no network jitter. Zero network round-trip. Best-in-class for latency-sensitive interactions. |
| **Simultaneous delivery** | Easy. Text arrives from cloud, TTS starts immediately on-device. No additional network hop. Visual render and TTS both triggered locally. |
| **Cost** | Zero marginal cost. Compute runs on user's device. |
| **Offline** | Full TTS works offline (if the reasoning output was cached or pre-generated). Pure offline mode is still limited by cloud-dependent reasoning. |
| **Privacy** | Maximum. Narration text never leaves the device for speech synthesis. |
| **Complexity** | Moderate-high. Must bundle and manage a ~80–200MB model in the Flutter app. Model updates require app updates (or a model-serving layer). Must handle device fragmentation — low-end Android devices may struggle with real-time TTS alongside the main app rendering. Battery impact on sustained voice sessions. |

**The CarrotFin-specific concern: expressiveness.**

CarrotFin's voice layer isn't just reading text aloud. The Composition agent produces `narration_type` values: `summary`, `annotation`, `silent`. The Conversational agent's voice output has `pacing_hints` and `sync_with_visual`. This requires expressiveness control — pauses, emphasis, tone shifts — that current 82M-parameter on-device models handle adequately for simple narration but struggle with for nuanced financial advisory delivery.

The "key insight" moment in Phase 3 ("The biggest thing people miss is the uninsured parent") needs to *land* emotionally. An edge SLM reads it accurately. A cloud model reads it *compellingly*.

**Verdict:** Excellent for V2+ when edge models mature. Problematic for V1 if the voice is meant to feel like a trusted advisor rather than a text reader. Best used as a fallback or for simple narrations today.

---

### Scenario D: Hybrid — Cloud Reasoning + Edge TTS with Cloud Upgrade Path

**Architecture:**
```
Agent Pipeline (cloud)
    │
    ├─→ Conversational response text ─→ TTS Router ─┬─→ Edge TTS (simple narrations)
    │                                                 └─→ Cloud TTS (emotional/complex)
    │
    └─→ Composition output ─→ visual render
         └─→ visual_narrations ─→ TTS Router ─┬─→ Edge TTS (annotations, factual)
                                               └─→ Cloud TTS (walkthrough summaries)
```

**How it works:** A `TTSRouter` in the Flutter app classifies each text-to-speech request and routes it to either the on-device model or the cloud TTS API. Simple, factual narrations (attribution strip walkthrough, confirmation echoes) use edge TTS. Emotional, editorial narrations (key insight delivery, encouragement copy, challenge responses) use cloud TTS.

**Routing logic (content-aware):**

| Content Type | Route | Rationale |
|:---|:---|:---|
| Confirmation echoes ("80 thousand a month — that right?") | Edge | Short, factual, latency-critical. Edge delivers sub-200ms. |
| Visual annotations ("This shows your 3-layer allocation...") | Edge | Descriptive, structural, no emotional weight. |
| Conversational editorial ("The biggest thing people miss is...") | Cloud | Emotional delivery matters. This is the AI's personality. |
| Phase-gate narration (reveal moment, Target Card) | Cloud | High-stakes moment. Worth the latency investment for expressiveness. |
| Challenge defense ("Your single-earner status and uninsured parent...") | Cloud | Credibility depends on confident, measured delivery. |
| Number sequences ("₹4,20,000 over 8 months") | Edge | Numbers need accuracy, not emotion. Edge handles this cleanly. |

| Dimension | Assessment |
|:---|:---|
| **Voice quality** | Best of both worlds. Emotional moments get cloud expressiveness. Factual moments get edge speed. |
| **Indian English** | Cloud handles the hard cases. Edge handles simple phonetics. Financial terms can be validated per-route. |
| **Latency** | ~70% of TTS calls hit edge (sub-200ms). ~30% hit cloud (~200–400ms streaming). Average turn latency drops significantly vs. cloud-only. |
| **Simultaneous delivery** | Edge TTS starts instantly. Cloud streams with ~100ms TTFB. Visual sync is easier when most narrations are local. |
| **Cost** | Edge calls are free. Cloud calls are reduced by ~70%. Free tier covers the reduced volume for longer. |
| **Offline** | Edge TTS works offline for simple narrations. Complex narrations fall back to text-on-screen (acceptable per DD04). |
| **Privacy** | Factual/numeric narrations stay on device. Only editorial/emotional text goes to cloud. D8 (health) data stays in edge-only path. |
| **Complexity** | Highest. Must build and maintain: router logic, two TTS engines, model bundling, fallback chain, voice consistency across engines (ensuring edge and cloud voices sound like the same "person"). |

**The voice consistency challenge:**

If edge TTS uses a different voice model than cloud TTS, the AI sounds like two different people within the same turn. This breaks the "talking to your advisor" illusion. Solutions:
1. **Voice cloning:** Clone the cloud voice into the edge model. Feasible with some open-source models but quality varies.
2. **Single-source voice:** Use a cloud provider's voice that also has an on-device variant (e.g., if Google offers Gemini Nano with the same voice as Flash TTS). Not currently available as a matched pair. [as of 2026-04]
3. **Accept the gap:** Use a "utility voice" for edge (more neutral, clearly "system") and a "personality voice" for cloud (warm, advisory). Frame it as intentional — system confirmations sound different from the AI's personal insights. This is arguably good UX — it signals modality.

**Verdict:** Strongest architecture for V1 launch. Balances latency, cost, privacy, and quality. The routing logic adds complexity but maps well to the existing `narration_type` field in the Composition output — the infrastructure for content-aware routing is already in the contracts.

---

### Scenario E: Gemini Live API as Conversational Shell + Traditional Pipeline for Others

**Architecture:**
```
User audio ──→ Gemini Live API (Conversational Agent only)
                    │
                    ├──→ textual output (extracted dimensions, state signals, response text)
                    │         ↓
                    │    State → Computation → Composition → visual render
                    │
                    └──→ native audio output (Conversational response, streamed directly to speaker)
                              ↓
                         Composition's visual_narrations → Edge or Cloud TTS (separate)
```

**How it works:** The Live API is used ONLY for the Conversational agent's voice I/O. The user speaks, the Live API reasons and speaks back natively (no STT→text→TTS pipeline for the conversational response). Meanwhile, it also outputs structured text (extracted dimensions, state signals) that feeds the traditional agent pipeline for State → Computation → Composition. The Composition agent's `visual_narrations` are handled by a separate TTS path (edge or cloud, per Scenario D).

| Dimension | Assessment |
|:---|:---|
| **Voice quality** | Best possible for the conversational response — native audio reasoning. Visual narrations use separate TTS (good to excellent depending on route). |
| **Indian English** | Live API adapts to user's accent in real time. Financial terms benefit from the model's reasoning context (it knows it's saying "lakhs," not "lacks"). |
| **Latency** | Conversational response: sub-300ms (native audio, no pipeline). Visual narrations: depends on edge/cloud routing. Total turn: fastest possible for the editorial voice. |
| **Simultaneous delivery** | Live API audio starts immediately. Visual render can begin as soon as the Composition agent completes — potentially while the Live API is still speaking. Natural stagger. |
| **Cost** | Live API is more expensive per-turn than text-based reasoning. But it replaces both the Conversational agent's LLM call AND the TTS call — potentially cost-neutral. |
| **Offline** | None for conversational voice. Edge TTS handles visual narrations offline. |
| **Privacy** | User's raw audio goes to Google (same for any voice input). Narration text for visual narrations can stay on-device via edge. |
| **Complexity** | Very high. Two fundamentally different audio pathways in the same turn. The Live API operates via persistent WebSocket — different protocol from the REST-based agent pipeline. The Conversational agent's `response_text` is now redundant in voice mode (the model speaks directly) but still needed for text mode and for screen display. Synchronisation between Live API audio and Composition-triggered visual render is the hardest engineering challenge. |

**The dual-voice problem:**

The Conversational agent (via Live API) sounds one way (native, adaptive, contextual). The Composition agent's visual narrations (via separate TTS) sound different. Within a single turn, the user hears two voices from the same "advisor." This is worse than Scenario D's version of the problem because the quality gap between native audio and TTS is more pronounced.

**Mitigation:** Don't narrate visuals in voice mode when the Live API is active. Let the Live API's conversational response serve as the only spoken output. Visual narrations become text annotations on screen. This simplifies the architecture at the cost of richness — but it aligns with DD04 Rule 3 ("simultaneous, not sequential" — if the AI is speaking the editorial insight, the screen carries the structural detail, and the user reads the visual component rather than hearing a second voice describe it).

**Verdict:** Highest ceiling, highest complexity. The "conversational shell" approach is elegant but creates a bifurcated audio architecture that's hard to maintain. Best evaluated as a V2/V3 option when the product has validated that voice is high-engagement and worth the engineering investment.

---

## Comparative Matrix

| | A: Cloud TTS | B: Unified Live | C: Edge SLM | D: Hybrid | E: Live + Pipeline |
|:---|:---|:---|:---|:---|:---|
| **V1 Readiness** | ✅ High | ❌ Arch mismatch | ⚠️ Quality gap | ✅ High | ⚠️ Complex |
| **Latency (TTS)** | 200–500ms | <100ms | 150–350ms | 150–400ms avg | <100ms conv / 150–400ms visual |
| **Voice Quality** | ★★★★★ | ★★★★★ | ★★★☆☆ | ★★★★☆ | ★★★★★ conv / ★★★★☆ visual |
| **Indian English** | ★★★★☆ | ★★★★★ | ★★☆☆☆ | ★★★★☆ | ★★★★★ conv / ★★★☆☆ visual |
| **Cost at Scale** | $$ | $$$ | Free | $ | $$ |
| **Privacy** | Low | Low | High | Medium | Low–Medium |
| **Offline** | ❌ | ❌ | ✅ | Partial | Partial |
| **Arch Complexity** | Low | Very High | Moderate | High | Very High |
| **Agent Contract Fit** | ✅ Clean | ❌ Requires rewrite | ✅ Clean | ✅ Clean | ⚠️ Dual pathway |
| **Voice Consistency** | Single voice | Single voice | Single voice | ⚠️ Two voices | ⚠️ Two voices |

---

## Recommendation

### V1: Start with Scenario A (Cloud TTS), design for Scenario D (Hybrid)

**Why:**

1. **Voice is opt-in (DD04 Rule 1).** Most V1 users will use text+tap. The TTS layer's launch quality matters less than the reasoning quality. Don't over-engineer for a modality that < 30% of users may enable initially.

2. **Cloud TTS is architecturally clean.** It fits the existing Agent Invocation Contracts perfectly — text in, audio out, no contract changes. The `visual_narrations` and `voice_output` fields produce text that goes to an API. Simple.

3. **Gemini Flash TTS is effectively free at V1 scale.** Zero users means zero TTS costs. Free tier covers early beta. By the time cost matters, you'll have usage data to optimise.

4. **Design the routing interface now.** Build the `TTSRouter` abstraction in the Flutter app even for V1, but route 100% of calls to cloud. This is one day of engineering. When edge models mature (and they're maturing fast — MOS gap is ~0.1 as of 2026-04), swap the router logic without touching the agent pipeline.

### V2: Migrate to Scenario D (Hybrid) as edge models reach parity

**When:**
- Edge TTS Indian English accent quality validated against target users
- Financial terminology phoneme accuracy confirmed (SIP, EMI, DICGC, lakhs)
- Device fragmentation tested (low-end Android 3GB RAM devices)

**What changes:**
- `TTSRouter` starts routing factual/confirmation narrations to on-device model
- Emotional/editorial narrations stay on cloud
- D8 (health) narrations hard-routed to edge for privacy

### V3 (evaluate, don't commit): Scenario E if voice engagement > 50%

**If and only if:**
- Voice mode adoption exceeds 50% of active sessions
- User feedback indicates conversational naturalness is a top-3 priority
- The Live API's cost structure is sustainable at your user volume
- You're willing to manage the dual-audio-pathway complexity

---

## Impact on Agent Invocation Contracts

The recommended path (A → D → E) requires **no changes** to the v0.2.0 contracts:

| Contract Element | Impact |
|:---|:---|
| `ConversationalOutput.voice_output` | Text-based. TTS synthesis is downstream. Works with any scenario. |
| `CompositionOutput.visual_narrations` | Text-based. `narration_type` field directly maps to the Scenario D routing logic. |
| `active_modality` | Already propagated to both agents. Router reads this to decide if TTS is needed at all. |
| `sync_with_visual` / `sync_point` | Remain valid — they govern *timing*, not synthesis method. |

The only contract extension needed if Scenario E is pursued:

```
ConversationalOutput {
  // NEW (Scenario E only)
  native_audio_stream: AudioStream | null    // If Live API is active, raw audio output
                                              // replaces voice_output text-to-speech path
}
```

This is additive — existing contracts don't break.

---

## Open Questions

1. **Voice consistency across edge + cloud.** Can we find or train an edge model that sounds sufficiently similar to Gemini Flash TTS to avoid the "two advisors" perception? This needs a prototype and user testing — not a desk analysis.

2. **Indian English financial terminology.** No research found validated pronunciation quality for "lakhs," "SIP," "DICGC" across any edge model. This needs hands-on evaluation with a prototype generating 50+ financial sentences.

3. **Flutter model bundling size impact.** Kokoro-82M is ~150MB. Gemma 3n for audio may be larger. What's the acceptable app size increase for a feature only ~30% of users may enable? Consider on-demand model download (first voice enable triggers download).

4. **Battery impact of sustained edge TTS.** A Phase 2 Assessment session is 10–15 minutes of voice interaction. What's the battery drain of running a neural TTS model continuously alongside the main app? Low-end Android devices are the constraint.

---

> *This research informs Open Question #8 in the Agent Invocation Contracts (v0.2.0). The recommended approach preserves contract integrity across all scenarios. Revisit when edge Indian English TTS quality can be validated against CarrotFin's financial vocabulary requirements.*

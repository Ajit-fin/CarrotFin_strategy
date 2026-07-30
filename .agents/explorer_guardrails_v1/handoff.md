# Guardrails Compatibility Evaluation & Architecture Report

## Executive Summary
This report evaluates the shared `guardrails_v1.xml` module against the three core prompt files: `pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`, and `flash_conversation_v1.xml`. 

We identified critical gaps in guardrail injection, trust terminology misalignment, privacy exposure risks, legacy voice-interaction assumptions, and structural redundancies in goal management. Implementing the proposed text-only adaptations and modular extractions will reduce token footprint, align the prompts with the CarrotFin design principles (Adaptive Composition, Conversational + Visual Integration), and prevent compliance or privacy failures in the text-only MVP.

---

## 1. Observation

### Guardrails Module (`guardrails_v1.xml`)
* **Advisory & Computation Boundaries:** Dictates advisory limits and requires Deterministic Compute Engine (DCE) for all math.
* **Privacy Rules (Lines 39-41):** 
  ```xml
  39:   - Reference data by CATEGORY ("given your income level") not exact values — unless the user explicitly stated the number in this turn. The screen shows numbers; you narrate the meaning.
  40:   - Sensitive fields (health, exact salary) require opt-in or text-input fallback. Never echo sensitive data back.
  ```
* **Trust Gating (Lines 30-33):**
  ```xml
  30:   Trust gating:
  31:     NEW     → max 1-2 fields per ask. Each follows value delivery.
  32:     WARMING → related data clusters (3-5 fields).
  33:     TRUSTING → sensitive data requests and automated actions permitted.
  ```

### Detail Planner (`pro_detail_planner_v1.xml`)
* **Voice Reference (Line 42):** Imports the voice module: `<moduleRef id="voice" version="1.0" path="modules/voice_v1.xml" />`
* **Trust Calibration (Lines 297-298):** Refers to binary trust levels:
  ```xml
  297:   - Low trust → stricter value-before-ask cadence
  298:   - High trust → denser data collection per step
  ```
* **Goal Handling (Lines 354-409):** Emits `goalMutation` (CREATE/UPDATE) with structured `identity`, `target`, and `goal_context` sections in §7.
* **Missing Constraints:** No instructions in its `<constraints>` block (Lines 354-362) referencing the privacy constraints (no-echoing or referencing by category) defined in `guardrails_v1.xml`.

### Overview Planner (`pro_overview_planner_v1.xml`)
* **Voice Reference (Line 40):** Imports the voice module: `<moduleRef id="voice" version="1.0" path="modules/voice_v1.xml" />`
* **Complexity Adaptation (Lines 172-176):** Contains custom rules mapping profile characteristics to phase structure complexity.

### Flash Conversation (`flash_conversation_v1.xml`)
* **Missing Guardrails:** Does not import or reference `guardrails_v1.xml` in its `<systemPrompt>` or its `<contextInjectionSpec>` (Lines 419-434).
* **Voice Terminology:**
  * Line 38: `"You are the face, the voice, the relationship."`
  * Line 82: `"...message the user reads/hears."`
* **Goal Handling (Lines 203-259):** Implements a redundant "Goal Protocol" section describing goal crystallization, updates, and lifecycle commands.

### Voice Module (`modules/voice_v1.xml`)
* **Tone Calibration (Lines 18-33):** Maps `emotionalState`, `trustLevel` (using `NEW`/`WARMING`/`TRUSTING` values), and `literacyLevel` to conversational tone rules.
* **Language Rules (Lines 34-39):** Specifies Indian financial terminology (₹, lakhs/crores) and projection qualifying rules.

---

## 2. Logic Chain

### Gap 1: Guardrails Bypass in Companion Conversation
* **Observation:** `flash_conversation_v1.xml` does not import or inject `guardrails_v1.xml`.
* **Reasoning:** Since `flash_conversation` is the primary conversational front-end (handling Discovery and Direct Answers), the lack of guardrail ingestion means the model is unaware of the strict prohibition against naming specific funds, banks, or products when responding to user Q&A.
* **Conclusion:** This exposes CarrotFin to severe compliance risks (e.g. accidentally recommending a specific AMC in a direct query) and breaks the trust gating logic on initial queries.

### Gap 2: Trust Architecture Terminology Mismatch
* **Observation:** `guardrails_v1.xml` defines trust states as `NEW`, `WARMING`, and `TRUSTING`. `pro_detail_planner_v1.xml` references `Low trust` and `High trust`. `voice_v1.xml` uses `NEW`, `WARMING`, `TRUSTING` values.
* **Reasoning:** The backend injects a user profile with a `trustLevel` dimension. If the Planner relies on a binary interpretation (`Low`/`High`) while the guardrails and voice guides rely on a three-tier system (`NEW`/`WARMING`/`TRUSTING`), step generation limits (e.g. maximum fields per step) cannot be consistently enforced by the Planner.
* **Conclusion:** The trust gating instructions are misaligned and will cause unpredictable form groupings during adaptive composition.

### Gap 3: Privacy/Echoing Violation Risk in Detail Planner
* **Observation:** `guardrails_v1.xml` forbids echoing sensitive inputs or referencing exact values in conversation. `flash_conversation_v1.xml` reinforces this rule. `pro_detail_planner_v1.xml` contains no such constraint.
* **Reasoning:** The Detail Planner generates `responseText` during execution steps (e.g. for `COMPUTE` steps). Without explicit constraints, it is prone to echoing raw sensitive data (e.g., exact salary, dependency costs) in the conversational text it outputs.
* **Conclusion:** This represents a leakage vector for sensitive user details, violating the privacy-by-design architecture.

### Gap 4: Legacy Voice Assumptions
* **Observation:** Consuming prompts import `voice_v1.xml`. Terminology in prompts refers to "hears", "voice", and "narrates". `guardrails_v1.xml` references a "text-input fallback". `voice_v1.xml` guides tone speed ("slower").
* **Reasoning:** Since CarrotFin MVP is strictly text-only, these voice-centric references prime the models to behave like speech-to-text systems. This causes them to avoid structured text features (like bullet points, markdown formatting, tables) and waste context tokens on "text-input fallback" logic.
* **Conclusion:** The prompt architecture must be purged of voice assumptions to optimize output for read-only readability and mobile screens.

### Gap 5: Architectural Redundancy in Goal Management and Adapting
* **Observation:** Both the Detail Planner and Flash Conversation prompts define duplicate logic for goal crystallization, goal portfolio updates, and lifecycle commands. Both Planners and the voice module duplicate the behavioral mapping table.
* **Reasoning:** Maintaining the same logic (like how to format a goal update or handle a minor goal change) in multiple prompt files increases engineering cost and risks logical drift as the product evolves.
* **Conclusion:** The goal protocol and profile mapping rules should be consolidated into their respective shared modules.

---

## 3. Caveats
* **Backend Ingestion:** We assumed the backend correctly implements the XML assembly by concatenating the referenced modules in the exact sequence described in the files.
* **UserProfile Schema:** We assumed the `userProfile.trustLevel` and `userProfile.literacyLevel` values are generated and populated by an upstream component.
* **Text-Only MVP Scope:** This analysis is strictly scoped to the decision to exclude voice features from the MVP. If voice is reintroduced, these recommendations would require adaptation.

---

## 4. Conclusion & Actionable Recommendations

### Recommendation 1: Fix Companion Compliance & Privacy
1. **Inject Guardrails in Companion:** Update `flash_conversation_v1.xml` to import the guardrails module:
   ```xml
   <moduleRef id="guardrails" version="1.0" path="modules/guardrails_v1.xml" />
   ```
2. **Add Privacy Constraints to Detail Planner:** In `pro_detail_planner_v1.xml` §6 (`<constraints>`), append:
   ```xml
   - PRIVACY: Never echo sensitive data back in responseText. Narrate the meaning of computed values and reference inputs by category ("your monthly surplus") rather than exact numbers, unless the user provided the exact value in the current turn.
   ```
3. **Standardize Trust Terminology:** Update `pro_detail_planner_v1.xml` to map its trust behaviors directly to the three-tier guardrail system:
   * `NEW` → Low density (1-2 fields), strict value-before-ask.
   * `WARMING` → Moderate density (3-5 fields).
   * `TRUSTING` → High density, sensitive fields allowed.

### Recommendation 2: Purge Voice Legacy (Adapt to Text-Only)
1. **Rename the Voice Module:** Rename `modules/voice_v1.xml` to `modules/persona_v1.xml`.
2. **Update Module References:** Change references in the Planner XMLs:
   ```xml
   <moduleRef id="persona" version="1.0" path="modules/persona_v1.xml" />
   ```
3. **Refactor Text-Only Terminology:**
   * In `flash_conversation_v1.xml`: Change "reads/hears" to "reads". Change "the face, the voice" to "the face, the persona".
   * In `guardrails_v1.xml`: Change "text-input fallback" (Line 40) to "masked security inputs or bypass option".
   * In the newly renamed `persona_v1.xml`: Change "emotionalState: ANXIOUS / OVERWHELMED → slower, gentler" to "use shorter paragraphs, bullet points, and high whitespace density to reduce visual cognitive load".

### Recommendation 3: Module Optimization (Extract Redundancies)
1. **Consolidate Goals Protocol:** Extract §7 of the Detail Planner and §9 of the Companion into `modules/goals_context_v1.xml` under a new `<goalsBehavioralRules>` tag:
   * Keep the schema definition and lifecycle state mapping in one file.
   * Prompts should only define the output schemas (`goalMutation`) and reference the shared module for the selection logic.
2. **Extract Adaptive Mapping:** Extract the userProfile adaptation guidelines (literacy, trust, anxiety) into `modules/persona_v1.xml`. Create a unified adaptation matrix so both the planners (for layout structure) and companion (for tone) reference the same definition.

---

## 5. Verification Method

### Structural Validation
1. **XML Schema Integrity:** Verify that all XML prompt files load correctly and are well-formed by parsing them with an XML processor.
2. **Reference Audit:** Run a grep check to verify that no prompts continue to import `modules/voice_v1.xml` and instead reference `modules/persona_v1.xml`.
3. **Terminology Audit:** Search for forbidden terms (`hears`, `voice`, `slower` in tone contexts) to confirm complete text-only compliance.

### Invalidation Conditions
This report is invalidated if:
* The backend team implements voice capabilities as part of the MVP.
* The two-prompt planner architecture is merged into a single system prompt, rendering separate companion/planning interfaces redundant.

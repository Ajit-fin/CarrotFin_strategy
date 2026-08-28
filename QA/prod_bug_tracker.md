# Mobile App Production Testing - Bug & Issue Tracker

> [!NOTE]
> This tracker logs bugs, UI/UX polish items, prompt refiner issues, and product suggestions captured during mobile production testing.

---

## 🏷️ Guidelines & Classification Legends

### Priority Levels
| Priority | Level | Description / SLA |
| :--- | :--- | :--- |
| 🔴 **P0** | **Blocker / Critical** | anything that blocks immediate launch to friends |
| 🟠 **P1** | **High** | anything that blocks launch to general network of firends |
| 🟡 **P2** | **Medium** | long term before open release |
| 🔵 **P3** | **Low / Polish** | long term vision |

---

## 📋 Production Bug & Issue Tracker Table

| ID | Date | Type | Priority | Issue Description (Expected vs Actual & Details) | Screenshot Ref | Status | Assignee / Action Items |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `QA-001` | 2026-08-08 | UX / Feature | 🟠 P1 | **Persuasive Emergency Fund (EF) Explanation**<br>• *Problem:* Most users don't know what an EF is, why to have one, or outright discard the concept.<br>• *Requirement:* Need a persuasive, simple, and adaptive explanation tailored to user nuances.<br>• *Format Hierarchy:* **Graphical representations (1st choice)** > **Tables (2nd choice)** > Plain Text (least preferred). | `Pending upload` | 🟡 Open |  |
| `QA-002` | 2026-08-08 | AI Persona / Feature | 🟠 P1 | **Dynamic User Profile & Brainstorming Companion**<br>• *Goal:* Build a dynamic behavioral profile so the AI tunes into each user's style (e.g. challenging partner vs comforting partner, switching on demand).<br>• *Requirement:* Enable smooth automatic and user-initiated adaptation. | `N/A` | 🟡 Open |  |
| `QA-003` | 2026-08-08 | UI Bug | 🔴 P0 | **Mobile Width & Text Card Overflow**<br>• *Actual:* The "detail" explanation in `INLINE_INSIGHT` overflowed the mobile screen width and card boundaries. Sentences across multiple rows failed to wrap.<br>• *Expected:* container was within mobile view port width but the text was not | `Pending upload` | 🟡 Open |  |
| `QA-004` | 2026-08-08 | Prompt / Flow | 🔴 P0 | **Repetitive Safety Net Context in Consecutive Turns**<br>• *Actual:* Transitioning from "What is EF" to "Help build EF" (Flash / Refiner prompt) repeated India-specific missing safety net points in consecutive turns.<br>• *Expected:* Advance the conversation smoothly without repeating already established context. | `N/A` | 🟡 Open |  |
| `QA-005` | 2026-08-08 | UI/UX Polish | 🟠 P1 | **Bottom App Bar Screen Real Estate**<br>• *Actual:* The bottom app bar positioned below the message box takes up significant vertical screen space in text mode.<br>• *Expected:* Maximize chat viewport. | `Pending upload` | 🟡 Open |  |
| `QA-006` | 2026-08-08 | UI / Stream Bug | 🔴 P0 | **Mid-Streaming CTA Click / Stream Abandonment Breaks Chat**<br>• *Actual:* Tapping a CTA while an AI response is actively streaming or leaving/backgrounding mid-stream causes CTA buttons to vanish permanently and breaks chat input state.<br>• *Expected:* Chat interaction should not break when CTA is clicked mid-streaming. | `Pending upload` | 🟡 Open |  |
| `QA-007` | 2026-08-08 | UI/UX Bug | 🟠 P1 | **Intermittent Missing CTAs in Plan Overview Card**<br>• *Actual:* Plan Overview cards periodically render without action CTAs (e.g., Next Steps or Modify Plan action buttons missing).<br>• *Expected:* Plan Overview cards should always display expected CTAs. | `Pending upload` | 🟡 Open |  |
| `QA-008` | 2026-08-08 | Prompt / Flow | 🟠 P1 | **Prepended Q&A Answer Repetition on Plan Overview Re-entry**<br>• *Actual:* Navigating from Plan Overview Card -> Q&A sub-flow -> back to Plan Overview Card prepends the prior Q&A answer text onto the new Plan Overview card header/content.<br>• *Expected:* Plan Overview card should not show text from the previous Q&A. | `N/A` | 🟡 Open |  |
| `QA-009` | 2026-08-08 | Interaction Bug | 🔴 P0 | **Form Dismiss (X) Triggers False Confirm & Launches Next Form**<br>• *Actual:* Dismissing/closing a form modal via 'X' without selecting any option fires a "Confirm" event payload to the backend, immediately launching the subsequent form.<br>• *Expected:* Tapping 'X' should cancel the form step. | `Pending upload` | 🟡 Open |  |
| `QA-010` | 2026-08-08 | UI / Schema Bug | 🔴 P0 | **Form / Input Headers Rendered in Raw camelCase / snake_case**<br>• *Actual:* Form title and single input field headers appear as raw identifiers (e.g. `emergencyFundGoal` or small casing with `_`).<br>• *Expected:* Display human-readable title casing. | `Pending upload` | 🟡 Open |  |
| `QA-011` | 2026-08-08 | UI / Text Parsing Bug | 🟠 P1 | **Partial Markdown Formatting Breakdown (`**bold**` & `\n\n` Spacing)**<br>• *Actual:* Markdown formatting breaks partially during streaming/rendering—bold text tags (`**`) fail to parse and literal `\n\n` escape sequences display in chat text instead of paragraph breaks. Note: this could be wrong markdown generation by llm also.<br>• *Expected:* Text should render cleanly with correct formatting and spacing. | `Pending upload` | 🟡 Open |  |
| `QA-012` | 2026-08-08 | Interaction / State Bug | 🔴 P0 | **Form Persistence Across Subsequent Conversation Turns**<br>• *Actual:* not full modal. the form in collapsed state but still active while the chat conversation continues for a few turns underneath.<br>• *Expected:* Form must close upon option selection. | `Pending upload` | 🟡 Open |  |
| `QA-013` | 2026-08-08 | Input Validation / UX Bug | 🟡 P2 | **Count-Specific Numeric Fields Allow Decimal Input**<br>• *Actual:* Input fields meant for discrete count values (e.g., number of children or dependents) accept decimal entries (e.g., `2.5`).<br>• *Expected:* Count-based numeric fields should restrict keyboard input to non negative integers. | `Pending upload` | 🟡 Open |  |
| `QA-014` | 2026-08-08 | Prompt / Context Flow Bug | 🟠 P1 | **Immediate Repetition of Children Question Post-Form Submission**<br>• *Actual:* Right after submitting a form with children/dependents details, the chat AI asks the exact same question about children again in conversational text.<br>• *Expected:* The AI should not repeat questions just answered in the form. | `N/A` | 🟡 Open |  |
| `QA-015` | 2026-08-08 | UX / GenUI Content Bug | 🔴 P0 | **Inappropriate System-Assigned User Message on Form Submit**<br>• *Actual:* When a user submits a form, the system-assigned message in the chat is inappropriate or robotic, mostly just saying "Confirm Counts" or "Confirm".<br>• *Expected:* System-generated user messages after form submission should be natural and contextually appropriate. | `Pending upload` | 🟡 Open |  |
| `QA-016` | 2026-08-08 | State / UI Bug | 🔴 P0 | **Inactive Forms Reactivate on Chat Re-entry**<br>• *Actual:* Navigating away (closing chat) and returning causes previously submitted forms to change from a greyed inactive state back to an active (but collapsed) state.<br>• *Expected:* Previously filled forms should remain in an inactive, greyed-out state upon chat re-entry. | `Pending upload` | 🟡 Open |  |
| `QA-017` | 2026-08-08 | UI / UX Bug | 🔴 P0 | **Incorrect Input Component for Income (Fixed Slider)**<br>• *Actual:* Income input uses a fixed range slider rather than a free-range numeric input, restricting user entry.<br>• *Expected:* Provide a free-range numeric input component for income fields. | `Pending upload` | 🟡 Open |  |
| `QA-018` | 2026-08-08 | UI / Content Bug | 🔴 P0 | **Missing Sub-titles/Descriptions on Form Inputs**<br>• *Actual:* Input components only display the form title and input header (e.g., Form: Family Details, Header: Children under 5) but lack any description or sub-title for extra context.<br>• *Expected:* Input components should display descriptive sub-titles to guide the user when necessary. | `Pending upload` | 🟡 Open |  |

---

## 📊 Summary & Status Counts

| Status | P0 (Blocker) | P1 (High) | P2 (Medium) | P3 (Low) | Total |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Open** | 10 | 7 | 1 | 0 | **18** |
| **In Progress** | 0 | 0 | 0 | 0 | **0** |
| **Resolved** | 0 | 0 | 0 | 0 | **0** |
| **Total** | **10** | **7** | **1** | **0** | **18** |

# Pending Prompt & Schema Updates

*This is a temporary record of planned updates to the AI prompts and schemas. These changes are approved in principle but pending execution.*

---

## 1. Constraint: Single Visual Component Per Turn

**Context:** 
We need to prevent the LLM from emitting multiple non-text components (cards, forms, etc.) in a single turn, as stacking multiple visual cards creates a poor, cluttered user experience on the frontend.

**Planned Edits:**

1. **Hard Schema Enforcement:**
   * **Target:** `flash_conversation_v1.xml`, `pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml`
   * **Action:** Add `"maxItems": 2` to the `uiDirective.components` array definition within the JSON schema. This physically blocks the model via the Structured Output API from generating more than two components (1 `RESPONSE_TEXT` + 1 visual component).

2. **Prompt Copy Adjustments:**
   * **Target:** `flash_conversation_v1.xml`, `pro_detail_planner_v1.xml`, `pro_overview_planner_v1.xml` (within `<responseSchema>`)
   * **Action:** Update the `uiDirective.components` description to explicitly state: *"Maximum 2 items total: typically one RESPONSE_TEXT and at most ONE visual component. Never emit multiple visual cards in a single turn."*
   
   * **Target:** `component-palette-openapi.json` (top-level `description` field — OpenAPI format has no separate `howToUse.principle`)
   * **Action:** Append to the description: *"you MUST append at most ONE additional visual component... Do not stack multiple visual components."*

3. **Multi-Input Fallback (FORM_GROUP):**
   * **Target:** `flash_conversation_v1.xml`, `pro_detail_planner_v1.xml` (within system prompt rules)
   * **Action:** Reinforce the instruction that if the LLM needs to collect multiple data points in a single turn, it MUST use the composite `FORM_GROUP` component rather than attempting to stack multiple `VALUE_INPUT` components.

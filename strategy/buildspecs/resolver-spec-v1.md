# Resolver Specification v1 — uiIntent → uiDirective

> **Version:** 1.0.0  
> **Date:** 2026-08-13  
> **Status:** Draft — testing phase  
> **Lineage:** [uiIntent_v2_architecture](conversation://098364c6-952a-4223-b28c-96f6e2656e3e) → [uiIntent-schema-v1.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/uiIntent-schema-v1.json) → this spec  
> **Audience:** Backend engineers implementing the resolver service

---

## 1. Purpose

The Resolver is a **deterministic backend service** that transforms `uiIntent` (the LLM's semantic UI intent output) into `uiDirective.components[]` (fully hydrated palette component JSON). It decouples the LLM from palette schema knowledge, saving ~3-5K tokens/turn.

**The LLM says *what* to show. The Resolver decides *how* to show it.**

---

## 2. Input / Output Contract

### 2.1 Resolver Inputs

| Input | Source | Description |
|---|---|---|
| `uiIntent` | LLM response | Semantic intent: `message`, `collect`, `present` per [uiIntent-schema-v1.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/uiIntent-schema-v1.json) |
| `fieldSchema` | Static registry | [profile-fields-schema-v2.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/profile-fields-schema-v2.json) — field definitions with `inputType`, `min/max/step`, `enumValues/enumLabels/enumIcons`, `smartDefault`, `skipConfig` |
| `entitySummaries` | Session state | `knownEntitySummaries` — map of `entityGroupId` → `{ firstName, relationshipToUser, disambiguatingFacts }` |
| `journeyContext` | Session state | `{ journeyId, currentPhaseId, currentStepAction }` — for `guidanceMarker` derivation |
| `planState` | Session state | Current `planObject` — for milestone/allocation data already computed |

### 2.2 Resolver Output

```
uiDirective: {
  components: Component[]   // Per component-palette-openapi-v2.json
}
```

The output is an ordered array of palette components. The first component is always `RESPONSE_TEXT` (from `uiIntent.message`). Subsequent components are resolved from `collect` or `present`.

### 2.3 Invariants

- Every turn produces at least one component: `RESPONSE_TEXT`.
- A turn emits either `collect` components OR `present` components, never both (enforced by SINGLE-INTENT RULE in planner prompt; resolver should log a warning if both are present and process `collect` only).
- `fieldRef` and `jitField` are mutually exclusive per field entry. Exactly one must be non-null.

---

## 3. Top-Level Resolution Flow

```
resolve(uiIntent, fieldSchema, entitySummaries, journeyContext, planState):

  components = []

  // Step 1: RESPONSE_TEXT — always first
  components.push({
    componentId: "RESPONSE_TEXT",
    data: { text: uiIntent.message }
  })

  // Step 2: Collect resolution (input components)
  if uiIntent.collect != null:
    components.push(...resolveCollect(uiIntent.collect, fieldSchema, entitySummaries, journeyContext))

  // Step 3: Present resolution (display components)
  else if uiIntent.present != null:
    for each presentIntent in uiIntent.present:
      components.push(resolvePresent(presentIntent, entitySummaries, journeyContext))

  // Step 4: Deterministic repair pass
  for each component in components:
    repair(component, fieldSchema, entitySummaries)

  return { components }
```

---

## 4. Collect Resolution — Input Components

### 4.1 Entry Point

```
resolveCollect(collect, fieldSchema, entitySummaries, journeyContext):

  fields = collect.fields

  if fields.length == 1:
    return [resolveSingleField(fields[0], fieldSchema, entitySummaries, journeyContext)]

  if fields.length > 1:
    return [resolveFormGroup(collect, fieldSchema, entitySummaries, journeyContext)]
```

### 4.2 Single Field Resolution

```
resolveSingleField(field, fieldSchema, entitySummaries, journeyContext):

  schema = resolveFieldSchema(field, fieldSchema)
  // schema = fieldSchema[field.fieldRef] for known fields
  //        = synthesizeFromJitField(field.jitField) for JIT fields

  componentId, inputMode = selectInputComponent(schema, field)
  entityMeta = resolveEntityMeta(field.entityGroupId, entitySummaries)
  title = resolveTitle(field, schema, entityMeta, entitySummaries)
  action = resolveSubmitAction(journeyContext, field)

  if componentId == "VALUE_INPUT":
    return buildValueInput(field, schema, entityMeta, title, inputMode, action)

  if componentId == "TIERED_SELECTOR":
    return buildTieredSelector(field, schema, entityMeta, title, action)
```

### 4.3 Component Selection Table

The mapping from field `inputType` to palette component:

| `inputType` | Bounded (`min`+`max`)? | → `componentId` | → `inputType` (palette) | Notes |
|---|---|---|---|---|
| `CURRENCY` | Yes | `VALUE_INPUT` | `SLIDER` | Default for all profile currency fields |
| `CURRENCY` | No | `VALUE_INPUT` | `TEXT` | Rare — most currency fields have bounds |
| `NUMBER` | Yes | `VALUE_INPUT` | `SLIDER` | e.g., `targetRetirementAge` (35-80) |
| `NUMBER` | No | `VALUE_INPUT` | `TEXT` | Freeform numeric entry |
| `PERCENTAGE` | Always | `VALUE_INPUT` | `SLIDER` | Implicit bounds 0-100 |
| `DURATION` | Small option set (≤6) | `TIERED_SELECTOR` | chips | e.g., "3/6/9/12 months" |
| `DURATION` | General | `VALUE_INPUT` | `TEXT` | Freeform duration entry |
| `BOOLEAN` | Always | `TIERED_SELECTOR` | chips | Fixed: Yes/No items |
| `CHOICE_SIMPLE` | Always | `TIERED_SELECTOR` | chips | Flat chip mode — no per-item description |
| `CHOICE_TIERED` | Always | `TIERED_SELECTOR` | cards | Card mode — includes per-item description |
| `TEXT` | Always | `VALUE_INPUT` | `TEXT` | Freeform text entry |
| `DATE` | Always | `VALUE_INPUT` | `TEXT` | Date picker surfaces in frontend |

**Chip → Card Upgrade Rule:** If `optionDescriptions` is present on a `CHOICE_SIMPLE` field (provided by LLM in `uiIntent`), upgrade rendering mode from chips to cards. The `componentId` remains `TIERED_SELECTOR`; the presence of `description` on each item signals card mode to the frontend.

### 4.4 VALUE_INPUT Builder

```
buildValueInput(field, schema, entityMeta, title, inputMode, action):
  return {
    componentId: "VALUE_INPUT",
    entityMeta: entityMeta,
    data: {
      fieldKey: schema.fieldId,        // Always bare fieldId, never entity-prefixed
      title: title,
      description: field.description,  // Pass through from LLM, null if omitted
      exclusions: field.exclusions,    // Pass through from LLM, null if omitted
      inputType: inputMode,            // "SLIDER" or "TEXT"
      config: {
        unit: schema.unit,             // e.g., "INR", "YEARS", "%"
        period: schema.period,         // e.g., "MONTHLY", "ANNUAL", null
        min: schema.min,               // From field schema (or jitField)
        max: schema.max,
        step: schema.step,
        smartDefault: resolveSmartDefault(schema, entityMeta),
        placeholder: field.placeholder // Pass through from LLM for TEXT mode
      },
      submitAction: action
    }
  }
```

**`smartDefault` resolution priority:**
1. Collected profile data (if entity already has a prior value for related fields — e.g., use spouse's known income as default for spouse income re-ask)
2. `schema.smartDefault` from field schema (static median/mode values)
3. Midpoint of `min`/`max` range (last resort — what we're trying to avoid)

### 4.5 TIERED_SELECTOR Builder

```
buildTieredSelector(field, schema, entityMeta, title, action):

  items = []
  hasOptionDescriptions = field.optionDescriptions != null
    AND field.optionDescriptions.length == schema.enumValues.length

  if schema.inputType == "BOOLEAN":
    items = [
      { label: "Yes", value: "true" },
      { label: "No", value: "false" }
    ]
  else:
    for i in range(schema.enumValues.length):
      item = {
        label: schema.enumLabels[i],
        value: schema.enumValues[i]
      }
      // Card mode: attach per-item description
      if hasOptionDescriptions:
        item.description = field.optionDescriptions[i]
      // Emoji icons from field schema
      if schema.enumIcons and schema.enumIcons[i]:
        item.icon = schema.enumIcons[i]
      items.push(item)

  // Skip option from field schema
  skipOption = null
  skipAction = null
  if schema.skipConfig:
    skipOption = {
      label: schema.skipConfig.label,
      value: schema.skipConfig.value
    }
    skipAction = resolveSkipAction(journeyContext, field)

  return {
    componentId: "TIERED_SELECTOR",
    entityMeta: entityMeta,
    data: {
      fieldKey: schema.fieldId,
      title: title,
      description: field.description,
      items: items,
      skipOption: skipOption,
      submitAction: action,
      skipAction: skipAction
    }
  }
```

### 4.6 FORM_GROUP Builder

```
resolveFormGroup(collect, fieldSchema, entitySummaries, journeyContext):

  children = []
  for each field in collect.fields:
    child = resolveSingleField(field, fieldSchema, entitySummaries, journeyContext)
    child.data.submitAction = null   // Form owns the submit
    children.push(child)

  // Form title: LLM-provided, or infer from field facets
  formTitle = collect.formTitle
  if formTitle == null:
    formTitle = inferFormTitle(collect.fields, fieldSchema)

  // showTotal: auto-enable when all children are numeric with same unit
  showTotal = shouldShowTotal(children, fieldSchema)
  totalLabel = showTotal ? inferTotalLabel(collect.fields, fieldSchema) : null

  // Entity: use common entity across fields, or first field's entity
  entityMeta = resolveFormEntityMeta(collect.fields, entitySummaries)

  return {
    componentId: "FORM_GROUP",
    entityMeta: entityMeta,
    data: {
      title: formTitle,
      showTotal: showTotal,
      totalLabel: totalLabel,
      components: children,
      submitAction: resolveSubmitAction(journeyContext, collect.fields[0])
    }
  }
```

**Form title inference (fallback when LLM omits):**
- All fields share facet `EXPENSE` → "Monthly Expenses"
- All fields share facet `INCOME` → "Income Details"
- All fields share facet `ASSET` → "Savings & Investments"
- All fields share facet `DEBT` → "Debt Overview"
- All fields share facet `DEMOGRAPHICS` → "Family Details"
- Mixed facets → "Your Details"

**`showTotal` heuristic:**
- All children are `VALUE_INPUT` with `inputType: SLIDER` or `TEXT`
- All children share the same `config.unit` (e.g., all INR)
- All children share the same `config.period` (e.g., all MONTHLY)

---

## 5. Present Resolution — Display Components

### 5.1 resultKind → componentId Mapping

| `resultKind` | → `componentId` | Content mapping |
|---|---|---|
| `KPI` | `REVIEW_CARD` | `items[]` → `attribution.factors[]` |
| `PHASE_GATE` | `REVIEW_CARD` | `items[]` → `rows[]` |
| `STATUS` | `REVIEW_CARD` | `items[]` → `rows[]` or `attribution.factors[]` |
| `CONFIRMATION` | `REVIEW_CARD` | `items[]` → `rows[]` |
| `ALLOCATION` | `ALLOCATION_BAR` | `items[]` → `layers[]` |
| `PLAN` | `MILESTONE_PLAN` | `items[]` → `milestones[]` |

### 5.2 REVIEW_CARD Resolution

```
resolvePresent_ReviewCard(intent, entitySummaries, journeyContext):

  data = {
    title: intent.title,
    icon: intent.icon
  }

  // Headline pass-through (LLM-authored content)
  if intent.headline:
    data.headline = {
      value: intent.headline.value,
      subtitle: intent.headline.subtitle
    }

  // Status badge pass-through
  if intent.statusBadge:
    data.statusBadge = {
      label: intent.statusBadge.label,
      tone: intent.statusBadge.statusTone
    }

  // Items → rows or attribution (based on resultKind)
  if intent.resultKind == "KPI":
    data.attribution = resolveAttribution(intent)

  else if intent.resultKind in ["PHASE_GATE", "STATUS", "CONFIRMATION"]:
    data.rows = resolveRows(intent)

  // CTAs
  resolvePresentCtas(intent, data, journeyContext)

  entityMeta = resolveEntityMeta(intent.entityGroupId, entitySummaries) if intent.entityGroupId else null

  return {
    componentId: "REVIEW_CARD",
    entityMeta: entityMeta,
    data: data
  }
```

#### 5.2.1 Attribution Resolution (KPI)

Maps unified `items[]` to the palette's `attribution` structure:

```
resolveAttribution(intent):
  return {
    header: intent.itemsHeader ?? "How we got here",
    factors: intent.items.map(item => ({
      label: item.label,
      impact: item.value,               // e.g., "+2 months"
      direction: item.indicator ?? "NEUTRAL",  // UP, DOWN, NEUTRAL
      detail: item.detail               // One-line explanation
    }))
  }
```

#### 5.2.2 Rows Resolution (PHASE_GATE / STATUS / CONFIRMATION)

Maps unified `items[]` to the palette's `rows[]` structure:

```
resolveRows(intent):
  return intent.items.map(item => ({
    fieldKey: item.key ?? item.label,   // PHASE_GATE provides key; others use label
    label: item.label,
    displayValue: item.value,
    fieldType: deriveFieldType(item, intent.resultKind)
  }))
```

**`fieldType` derivation:**

```
deriveFieldType(item, resultKind):
  if resultKind == "PHASE_GATE" and item.key:
    schema = fieldSchema[item.key]
    if schema:
      if schema.inputType in ["CURRENCY", "NUMBER", "PERCENTAGE"]:
        return "NUMERIC"
      if schema.inputType in ["CHOICE_TIERED"]:
        return "TIERED"
      return "CATEGORICAL"
  // STATUS and CONFIRMATION items: infer from value format
  if item.value matches /^₹/ or item.value matches /^\d/:
    return "NUMERIC"
  return "CATEGORICAL"
```

### 5.3 ALLOCATION_BAR Resolution

Maps unified `items[]` to the palette's `layers[]` structure:

```
resolvePresent_AllocationBar(intent, entitySummaries, journeyContext):

  // Color palette for allocation segments (deterministic sequence)
  ALLOCATION_COLORS = [
    "var(--color-teal-primary)",       // #3CDDC7
    "var(--color-accent-blue)",         // #4A9FD9
    "var(--color-accent-amber)",        // #F5A623
    "var(--color-accent-coral)",        // #E8655A
    "var(--color-accent-purple)"        // #9B59B6
  ]

  layers = intent.items.map((item, index) => ({
    label: item.label,                  // Segment name
    amount: item.value,                 // Formatted amount, e.g., "₹80,000"
    displayLabel: item.shortLabel ?? abbreviate(item.label, 8),  // Bar label
    color: ALLOCATION_COLORS[index % ALLOCATION_COLORS.length],
    icon: item.icon,                    // Emoji from LLM
    vehicle: null,                      // Extracted from item.detail if structured
    description: null,                  // Extracted from item.detail if structured
    detail: item.detail                 // MVP: single detail prose block
  }))

  data = {
    title: intent.title,
    icon: intent.icon,
    layers: layers
  }

  resolvePresentCtas(intent, data, journeyContext, "allocation")

  entityMeta = resolveEntityMeta(intent.entityGroupId, entitySummaries) if intent.entityGroupId else null

  return {
    componentId: "ALLOCATION_BAR",
    entityMeta: entityMeta,
    data: data
  }
```

### 5.4 MILESTONE_PLAN Resolution

Maps unified `items[]` to the palette's `milestones[]` structure:

```
resolvePresent_MilestonePlan(intent, entitySummaries, journeyContext):

  milestones = intent.items.map(item => ({
    label: item.label,                  // Milestone name
    date: item.value,                   // Projected date, e.g., "Jun 2027"
    amount: item.detail,                // Target amount, e.g., "₹48,000"
    status: item.indicator ?? "UPCOMING" // UPCOMING or REACHED
  }))

  // Headline: derived from planning context, not unified items
  headline = {
    fieldKey: resolveHeadlineFieldKey(intent, journeyContext),
    value: intent.headline.value,       // e.g., "₹8,000/month"
    rawValue: parseNumericValue(intent.headline.value),
    cadence: inferCadence(intent.headline.value),  // "MONTHLY", "WEEKLY", etc.
    editable: false                     // Default false; inline edit deferred post-MVP
  }

  data = {
    title: intent.title,
    icon: intent.icon,
    headline: headline,
    milestoneHeader: intent.itemsHeader ?? "Your milestones",
    milestones: milestones
  }

  resolvePresentCtas(intent, data, journeyContext, "milestone")

  entityMeta = resolveEntityMeta(intent.entityGroupId, entitySummaries) if intent.entityGroupId else null

  return {
    componentId: "MILESTONE_PLAN",
    entityMeta: entityMeta,
    data: data
  }
```

**`cadence` inference from formatted value:**
- Value ends in `/month` → `"MONTHLY"`
- Value ends in `/week` → `"WEEKLY"`
- Value ends in `/year` → `"ANNUAL"`
- No cadence marker → `"MONTHLY"` (default)

**`rawValue` extraction:** Parse the numeric portion of `intent.headline.value`, stripping `₹`, commas, and cadence suffixes. E.g., `"₹8,000/month"` → `8000`.

---

## 6. CTA Resolution

### 6.1 Collect-Side CTAs

All input components require a `submitAction`. The resolver generates this from journey context:

```
resolveSubmitAction(journeyContext, field):
  return {
    label: "Continue",
    actionType: resolveActionType(journeyContext),
    uiAction: {
      uiActionType: "SUBMIT"
    },
    guidanceMarker: resolveGuidanceMarker(journeyContext, field)
  }
```

```
resolveSkipAction(journeyContext, field):
  return {
    label: "Skip",
    actionType: "STANDARD_COMPLETION",
    uiAction: {
      uiActionType: "SUBMIT"
    }
  }
```

### 6.2 Present-Side CTAs

```
resolvePresentCtas(intent, data, journeyContext, componentType = "review"):

  if intent.resultKind == "PHASE_GATE":
    // Phase-gate: confirmAction (primary) + editAction (secondary)
    if intent.primaryCta:
      data.confirmAction = resolveComponentAction(intent.primaryCta, journeyContext)
    else:
      data.confirmAction = defaultConfirmAction(journeyContext)

    if intent.secondaryCta:
      data.editAction = resolveComponentAction(intent.secondaryCta, journeyContext)
    else:
      data.editAction = defaultEditAction(journeyContext)

  else if componentType == "allocation":
    // ALLOCATION_BAR: primaryAction + optional secondaryAction
    if intent.primaryCta:
      data.primaryAction = resolveComponentAction(intent.primaryCta, journeyContext)
    else:
      data.primaryAction = defaultAcknowledgeAction(journeyContext)

    if intent.secondaryCta:
      data.secondaryAction = resolveComponentAction(intent.secondaryCta, journeyContext)

  else if componentType == "milestone":
    // MILESTONE_PLAN: primaryAction only
    if intent.primaryCta:
      data.primaryAction = resolveComponentAction(intent.primaryCta, journeyContext)
    else:
      data.primaryAction = defaultAcknowledgeAction(journeyContext)

  else:
    // REVIEW_CARD (non-phase-gate): acknowledgeAction + optional editAction + optional secondaryAction
    if intent.primaryCta:
      data.acknowledgeAction = resolveComponentAction(intent.primaryCta, journeyContext)
    else:
      data.acknowledgeAction = defaultAcknowledgeAction(journeyContext)

    if intent.secondaryCta:
      if intent.secondaryCta.intent == "EDIT":
        data.editAction = resolveComponentAction(intent.secondaryCta, journeyContext)
      else:
        data.secondaryAction = resolveComponentAction(intent.secondaryCta, journeyContext)
```

### 6.3 Intent → Action Mapping

| `intent` (from uiIntent CTA) | → `actionType` | → `uiActionType` | Default `label` |
|---|---|---|---|
| `CONFIRM` | `GUIDED_COMPLETION` | `SUBMIT` | "Looks good" |
| `ACKNOWLEDGE` | `STANDARD_COMPLETION` | `SUBMIT` | "Got it" |
| `EDIT` | `STANDARD_COMPLETION` | `FOCUS_CHAT_INPUT` | "I'd like to change something" |
| `EXPLORE` | `STANDARD_COMPLETION` | `FOCUS_CHAT_INPUT` | "Explore scenarios" |

```
resolveComponentAction(cta, journeyContext):
  mapping = INTENT_ACTION_MAP[cta.intent]
  return {
    label: cta.label ?? mapping.defaultLabel,
    actionType: mapping.actionType,
    uiAction: {
      uiActionType: mapping.uiActionType
    },
    guidanceMarker: (mapping.actionType == "GUIDED_COMPLETION")
      ? resolveGuidanceMarker(journeyContext, null)
      : null
  }
```

### 6.4 Guidance Marker Derivation

`guidanceMarker` routes the user's action to the correct backend handler:

```
resolveGuidanceMarker(journeyContext, field):
  // Format: {journeyId}:{phaseId}:{stepAction}
  // Populated from journeyContext when actionType is GUIDED_COMPLETION
  if journeyContext and journeyContext.journeyId:
    parts = [journeyContext.journeyId, journeyContext.currentPhaseId]
    if field and field.fieldRef:
      parts.push(field.fieldRef)
    else if journeyContext.currentStepAction:
      parts.push(journeyContext.currentStepAction)
    return parts.join(":")
  return null
```

### 6.5 Default CTA Builders

```
defaultConfirmAction(journeyContext):
  return {
    label: "Looks good",
    actionType: "GUIDED_COMPLETION",
    uiAction: { uiActionType: "SUBMIT" },
    guidanceMarker: resolveGuidanceMarker(journeyContext, null)
  }

defaultEditAction(journeyContext):
  return {
    label: "I'd like to change something",
    actionType: "STANDARD_COMPLETION",
    uiAction: { uiActionType: "FOCUS_CHAT_INPUT" }
  }

defaultAcknowledgeAction(journeyContext):
  return {
    label: "Got it",
    actionType: "STANDARD_COMPLETION",
    uiAction: { uiActionType: "SUBMIT" }
  }

resolveActionType(journeyContext):
  // Input collection during guided journeys uses GUIDED_COMPLETION
  if journeyContext and journeyContext.journeyId:
    return "GUIDED_COMPLETION"
  return "STANDARD_COMPLETION"
```

---

## 7. Label Resolution

Label resolution was previously embedded in the planner prompt. The resolver now owns this entirely.

### 7.1 Title Resolution

```
resolveTitle(field, schema, entityMeta, entitySummaries):

  // Priority 1: LLM-provided title (contextual override)
  if field.title:
    return field.title

  // Priority 2: Entity-qualified label from field schema
  baseLabel = schema.userFacingLabel    // e.g., "Monthly In-Hand Income"

  if entityMeta == null or entityMeta.relationshipToUser == "SELF":
    return baseLabel                    // No qualification needed

  if entityMeta.relationshipToUser == "HOUSEHOLD":
    return "Household " + baseLabel     // e.g., "Household Monthly Expenses"

  // Priority 3: Use entity's name if available
  entity = entitySummaries[entityMeta.entityGroupId]
  if entity and entity.disambiguatingFacts and entity.disambiguatingFacts.name:
    return entity.disambiguatingFacts.name + "'s " + baseLabel
    // e.g., "Priya's Monthly In-Hand Income"

  // Priority 4: Use relationship label
  return formatRelationship(entityMeta.relationshipToUser) + "'s " + baseLabel
  // e.g., "Spouse's Monthly In-Hand Income"
```

**`formatRelationship` mapping:**

| `relationshipToUser` | Display form |
|---|---|
| `SELF` | *(no prefix)* |
| `SPOUSE` | "Spouse" |
| `PARENT` | "Parent" |
| `CHILD` | "Child" |
| `SIBLING` | "Sibling" |
| `HOUSEHOLD` | "Household" |
| `OTHER` | "Dependent" |

### 7.2 Form Title Inference

Used as fallback when LLM omits `formTitle` for multi-field forms:

```
inferFormTitle(fields, fieldSchema):
  facets = unique(fields.map(f => fieldSchema[f.fieldRef]?.facet))
  if facets.length == 1:
    return FACET_TITLE_MAP[facets[0]]
  return "Your Details"

FACET_TITLE_MAP = {
  "EXPENSE": "Monthly Expenses",
  "INCOME": "Income Details",
  "ASSET": "Savings & Investments",
  "DEBT": "Debt Overview",
  "DEMOGRAPHICS": "Family Details",
  "PERSONAL": "Personal Details",
  "HEALTH": "Health Details",
  "INSURANCE": "Insurance Details",
  "TAX": "Tax Details",
  "BEHAVIORAL": "Financial Preferences"
}
```

---

## 8. Entity Threading

### 8.1 EntityMeta Resolution

```
resolveEntityMeta(entityGroupId, entitySummaries):
  if entityGroupId == null:
    return null

  entity = entitySummaries[entityGroupId]
  return {
    entityGroupId: entityGroupId,
    relationshipToUser: entity?.relationshipToUser ?? "SELF"
  }
```

### 8.2 Rules

1. `entityGroupId` is always passed through from `uiIntent` to the output component's `entityMeta.entityGroupId`.
2. `relationshipToUser` is looked up from `knownEntitySummaries` — never generated by the LLM.
3. `fieldKey` in output components is always **bare** (e.g., `"monthlyNetIncome"`, never `"person_2.monthlyNetIncome"`).
4. For `FORM_GROUP`: if all child fields share the same `entityGroupId`, the form inherits it. If mixed, use the first field's entity.

```
resolveFormEntityMeta(fields, entitySummaries):
  entityIds = unique(fields.map(f => f.entityGroupId))
  if entityIds.length == 1:
    return resolveEntityMeta(entityIds[0], entitySummaries)
  // Mixed entities — use first field's entity for the form wrapper
  return resolveEntityMeta(fields[0].entityGroupId, entitySummaries)
```

---

## 9. Field Schema Resolution

### 9.1 Known Fields (fieldRef)

```
resolveFieldSchema(field, fieldSchema):
  if field.fieldRef:
    schema = fieldSchema[field.fieldRef]
    if schema == null:
      error("Unknown fieldRef: " + field.fieldRef)
    return schema
  if field.jitField:
    return synthesizeFromJitField(field.jitField)
  error("Field must have either fieldRef or jitField")
```

### 9.2 JIT Field Synthesis

JIT fields carry inline schema from the LLM. The resolver synthesizes a field-schema-compatible object:

```
synthesizeFromJitField(jitField):
  schema = {
    fieldId: jitField.key,
    inputType: jitField.inputType,
    userFacingLabel: humanize(jitField.key),   // "expected_wedding_cost" → "Expected Wedding Cost"
    unit: jitField.unit,
    period: jitField.period,
    min: jitField.min,
    max: jitField.max,
    step: jitField.step
  }

  // Choice fields: map options to enumValues/enumLabels
  if jitField.options:
    schema.enumValues = jitField.options.map(o => o.value)
    schema.enumLabels = jitField.options.map(o => o.label)

  return schema
```

---

## 10. Deterministic Repair

After hydration, every component passes through a repair pipeline that validates against the palette's per-component `required` arrays and fills gaps from static defaults.

### 10.1 Repair Pipeline

```
repair(component, fieldSchema, entitySummaries):

  // 1. componentId valid?
  assert component.componentId in VALID_COMPONENT_IDS

  // 2. entityMeta present for input components?
  if component.componentId in ["VALUE_INPUT", "TIERED_SELECTOR", "FORM_GROUP"]:
    if component.entityMeta == null:
      component.entityMeta = { entityGroupId: "person_1", relationshipToUser: "SELF" }

  // 3. Data-level repairs per component type
  switch component.componentId:

    case "VALUE_INPUT":
      // title fallback
      if component.data.title == null:
        schema = fieldSchema[component.data.fieldKey]
        if schema:
          component.data.title = schema.userFacingLabel
      // slider config fallback
      if component.data.inputType == "SLIDER":
        schema = fieldSchema[component.data.fieldKey]
        if schema:
          config = component.data.config ?? {}
          config.min = config.min ?? schema.min
          config.max = config.max ?? schema.max
          config.step = config.step ?? schema.step
          config.smartDefault = config.smartDefault ?? schema.smartDefault
          config.unit = config.unit ?? schema.unit
          config.period = config.period ?? schema.period
          component.data.config = config
      // submitAction fallback
      if component.data.submitAction == null:
        component.data.submitAction = {
          label: "Continue",
          actionType: "STANDARD_COMPLETION",
          uiAction: { uiActionType: "SUBMIT" }
        }

    case "TIERED_SELECTOR":
      // items fallback
      if component.data.items == null or component.data.items.length == 0:
        schema = fieldSchema[component.data.fieldKey]
        if schema and schema.enumValues:
          component.data.items = buildSelectorItems(schema)
      // submitAction fallback
      if component.data.submitAction == null:
        component.data.submitAction = {
          label: "Continue",
          actionType: "STANDARD_COMPLETION",
          uiAction: { uiActionType: "SUBMIT" }
        }

    case "FORM_GROUP":
      // title fallback
      if component.data.title == null:
        component.data.title = "Your Details"
      // submitAction fallback
      if component.data.submitAction == null:
        component.data.submitAction = {
          label: "Continue",
          actionType: "STANDARD_COMPLETION",
          uiAction: { uiActionType: "SUBMIT" }
        }
      // Recurse into children
      for each child in component.data.components:
        repair(child, fieldSchema, entitySummaries)
        child.data.submitAction = null   // Enforce: form owns submit

    case "REVIEW_CARD":
      // No repair needed — content is LLM-authored
      pass

    case "ALLOCATION_BAR":
      // Ensure layers have displayLabel
      for each layer in component.data.layers:
        if layer.displayLabel == null:
          layer.displayLabel = abbreviate(layer.label, 8)

    case "MILESTONE_PLAN":
      // Ensure milestones have status
      for each milestone in component.data.milestones:
        if milestone.status == null:
          milestone.status = "UPCOMING"
```

### 10.2 Repair Defaults Summary

| Missing field | Component | Default value | Source |
|---|---|---|---|
| `entityMeta` | All input components | `{ person_1, SELF }` | Convention |
| `data.title` | `VALUE_INPUT` | `userFacingLabel` | Field schema |
| `config.min/max/step` | `VALUE_INPUT` (SLIDER) | Field schema values | Field schema |
| `config.smartDefault` | `VALUE_INPUT` (SLIDER) | `schema.smartDefault` | Field schema |
| `data.items` | `TIERED_SELECTOR` | `enumValues/enumLabels/enumIcons` | Field schema |
| `submitAction` | All input components | `{ "Continue", STANDARD_COMPLETION, SUBMIT }` | Convention |
| `data.title` | `FORM_GROUP` | `"Your Details"` | Convention |
| `layer.displayLabel` | `ALLOCATION_BAR` | Abbreviated `label` | Utility function |
| `milestone.status` | `MILESTONE_PLAN` | `"UPCOMING"` | Convention |

---

## 11. Helper Functions

### 11.1 Abbreviation

```
abbreviate(label, maxLength):
  if label.length <= maxLength:
    return label
  // Smart abbreviation: use first word or standard abbreviations
  words = label.split(" ")
  if words.length == 1:
    return label.substring(0, maxLength)
  // Try initials of multi-word labels
  return words.map(w => w[0].toUpperCase()).join("")
```

### 11.2 Numeric Value Parsing

```
parseNumericValue(formattedValue):
  // "₹8,000/month" → 8000
  // "₹2,40,000" → 240000
  // "82/100" → 82
  stripped = formattedValue.replace(/[₹,\/a-zA-Z\s]/g, "")
  return parseFloat(stripped)
```

### 11.3 Cadence Inference

```
inferCadence(formattedValue):
  lower = formattedValue.toLowerCase()
  if lower.includes("/month"): return "MONTHLY"
  if lower.includes("/week"): return "WEEKLY"
  if lower.includes("/year"): return "ANNUAL"
  if lower.includes("/quarter"): return "QUARTERLY"
  return "MONTHLY"   // Default
```

### 11.4 shouldShowTotal

```
shouldShowTotal(children, fieldSchema):
  // All children must be VALUE_INPUT
  if any(child.componentId != "VALUE_INPUT" for child in children):
    return false
  // All must share same unit and period
  units = unique(children.map(c => c.data.config?.unit))
  periods = unique(children.map(c => c.data.config?.period))
  return units.length == 1 and units[0] != null
    and periods.length == 1 and periods[0] != null
```

### 11.5 inferTotalLabel

```
inferTotalLabel(fields, fieldSchema):
  unit = fieldSchema[fields[0].fieldRef]?.unit
  period = fieldSchema[fields[0].fieldRef]?.period
  if unit == "INR" and period == "MONTHLY":
    return "Total Monthly"
  if unit == "INR" and period == "ANNUAL":
    return "Total Annual"
  return "Total"
```

### 11.6 humanize

```
humanize(snakeCaseKey):
  // "expected_wedding_cost" → "Expected Wedding Cost"
  return snakeCaseKey
    .split("_")
    .map(word => word[0].toUpperCase() + word.substring(1))
    .join(" ")
```

---

## 12. Error Handling

| Error condition | Resolver behavior |
|---|---|
| Unknown `fieldRef` | Log error, skip field, continue with remaining fields |
| Both `fieldRef` and `jitField` null | Log error, skip field |
| Both `fieldRef` and `jitField` non-null | Prefer `fieldRef`, log warning |
| Unknown `resultKind` | Log error, emit `REVIEW_CARD` as fallback with pass-through data |
| Both `collect` and `present` non-null | Log warning, process `collect` only (SINGLE-INTENT RULE) |
| Empty `collect.fields` array | Log warning, emit only `RESPONSE_TEXT` |
| `entityGroupId` not found in `entitySummaries` | Log warning, use `{ entityGroupId, relationshipToUser: "SELF" }` |
| `optionDescriptions` length ≠ `enumValues` length | Log warning, ignore `optionDescriptions` (fall back to chip mode) |

---

## 13. Implementation Notes

### 13.1 Service Architecture

The resolver should be a **stateless, synchronous function** called immediately after the LLM response is parsed and before the response is sent to the client. It requires no external I/O — all inputs (`fieldSchema`, `entitySummaries`, `journeyContext`, `planState`) are available in the existing request context.

### 13.2 Performance

The resolver performs dictionary lookups and array mappings only. Expected latency: <5ms per turn. No caching required.

### 13.3 Testing Strategy

Each component path should have unit tests covering:
- Known field resolution (fieldRef path)
- JIT field resolution (jitField path)
- Entity-qualified label generation (SELF, SPOUSE, named entity)
- Repair pipeline gap-filling
- CTA intent-to-action mapping
- Edge cases: empty fields, unknown fieldRef, mixed entities in FORM_GROUP

### 13.4 Versioning

- This spec targets `uiIntent-schema-v1.json` as input and `component-palette-openapi-v2.json` as output.
- When either schema evolves, this spec must be versioned accordingly.
- The resolver should validate input against the expected `uiIntent` schema version and reject mismatches.

---

## Appendix A: Complete Resolution Examples

### A.1 Single Currency Input (monthlyNetIncome)

**uiIntent input:**
```json
{
  "message": "What's your monthly take-home income, after taxes and deductions?",
  "collect": {
    "fields": [{
      "fieldRef": "monthlyNetIncome",
      "entityGroupId": "person_1",
      "description": "Your in-hand salary or net business income per month."
    }]
  }
}
```

**Resolver output:**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "What's your monthly take-home income, after taxes and deductions?" }
    },
    {
      "componentId": "VALUE_INPUT",
      "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
      "data": {
        "fieldKey": "monthlyNetIncome",
        "title": "Monthly In-Hand Income",
        "description": "Your in-hand salary or net business income per month.",
        "inputType": "SLIDER",
        "config": {
          "unit": "INR",
          "period": "MONTHLY",
          "min": 0,
          "max": 50000000,
          "step": 1000,
          "smartDefault": 50000
        },
        "submitAction": {
          "label": "Continue",
          "actionType": "GUIDED_COMPLETION",
          "uiAction": { "uiActionType": "SUBMIT" },
          "guidanceMarker": "emergency_fund:profile:monthlyNetIncome"
        }
      }
    }
  ]
}
```

### A.2 Choice Input (employmentType)

**uiIntent input:**
```json
{
  "message": "What type of work do you do?",
  "collect": {
    "fields": [{
      "fieldRef": "employmentType",
      "entityGroupId": "person_1"
    }]
  }
}
```

**Resolver output (abbreviated — items from field schema):**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "What type of work do you do?" }
    },
    {
      "componentId": "TIERED_SELECTOR",
      "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
      "data": {
        "fieldKey": "employmentType",
        "title": "Employment Type",
        "items": [
          { "label": "Salaried Govt", "value": "SALARIED_GOVT", "icon": "🏛️" },
          { "label": "Salaried Psu", "value": "SALARIED_PSU", "icon": "🏭" },
          { "label": "Salaried Large Private", "value": "SALARIED_LARGE_PRIVATE", "icon": "🏢" },
          "... (remaining items from field schema)"
        ],
        "submitAction": { "label": "Continue", "actionType": "GUIDED_COMPLETION", "uiAction": { "uiActionType": "SUBMIT" } }
      }
    }
  ]
}
```

### A.3 KPI Result (Emergency Fund Target)

**uiIntent input:**
```json
{
  "message": "Based on your expenses and risk factors, here's your personalized emergency fund target.",
  "present": [{
    "resultKind": "KPI",
    "title": "Your Emergency Fund Target",
    "icon": "🛡️",
    "headline": {
      "value": "₹2,40,000",
      "subtitle": "~6 months of essential expenses"
    },
    "itemsHeader": "How we got here",
    "items": [
      { "label": "High savings rate", "value": "+2 months", "indicator": "UP", "detail": "Your 40% savings rate provides a strong buffer" },
      { "label": "Single income household", "value": "+1 month", "indicator": "UP", "detail": "No secondary earner adds income risk" },
      { "label": "Stable employment", "value": "-1 month", "indicator": "DOWN", "detail": "Government job offers high stability" }
    ],
    "primaryCta": { "label": "Got it", "intent": "ACKNOWLEDGE" },
    "secondaryCta": { "label": "Adjust inputs", "intent": "EDIT" }
  }]
}
```

**Resolver output:**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "Based on your expenses and risk factors, here's your personalized emergency fund target." }
    },
    {
      "componentId": "REVIEW_CARD",
      "data": {
        "title": "Your Emergency Fund Target",
        "icon": "🛡️",
        "headline": {
          "value": "₹2,40,000",
          "subtitle": "~6 months of essential expenses"
        },
        "attribution": {
          "header": "How we got here",
          "factors": [
            { "label": "High savings rate", "impact": "+2 months", "direction": "UP", "detail": "Your 40% savings rate provides a strong buffer" },
            { "label": "Single income household", "impact": "+1 month", "direction": "UP", "detail": "No secondary earner adds income risk" },
            { "label": "Stable employment", "impact": "-1 month", "direction": "DOWN", "detail": "Government job offers high stability" }
          ]
        },
        "acknowledgeAction": {
          "label": "Got it",
          "actionType": "STANDARD_COMPLETION",
          "uiAction": { "uiActionType": "SUBMIT" }
        },
        "editAction": {
          "label": "Adjust inputs",
          "actionType": "STANDARD_COMPLETION",
          "uiAction": { "uiActionType": "FOCUS_CHAT_INPUT" }
        }
      }
    }
  ]
}
```

### A.4 Phase-Gate (Profile Review)

**uiIntent input:**
```json
{
  "message": "Let me confirm what I have so far. Please review your profile details.",
  "present": [{
    "resultKind": "PHASE_GATE",
    "title": "Your Profile",
    "items": [
      { "label": "Monthly Income", "value": "₹85,000", "key": "monthlyNetIncome" },
      { "label": "Monthly Rent", "value": "₹15,000", "key": "monthlyRent" },
      { "label": "Essential Expenses", "value": "₹25,000", "key": "monthlyNonDiscretionaryExpenses" },
      { "label": "Monthly EMIs", "value": "₹0", "key": "totalMonthlyEmi" }
    ],
    "primaryCta": { "label": "Looks good", "intent": "CONFIRM" },
    "secondaryCta": { "label": "I'd like to change something", "intent": "EDIT" }
  }]
}
```

**Resolver output:**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "Let me confirm what I have so far. Please review your profile details." }
    },
    {
      "componentId": "REVIEW_CARD",
      "data": {
        "title": "Your Profile",
        "rows": [
          { "fieldKey": "monthlyNetIncome", "label": "Monthly Income", "displayValue": "₹85,000", "fieldType": "NUMERIC" },
          { "fieldKey": "monthlyRent", "label": "Monthly Rent", "displayValue": "₹15,000", "fieldType": "NUMERIC" },
          { "fieldKey": "monthlyNonDiscretionaryExpenses", "label": "Essential Expenses", "displayValue": "₹25,000", "fieldType": "NUMERIC" },
          { "fieldKey": "totalMonthlyEmi", "label": "Monthly EMIs", "displayValue": "₹0", "fieldType": "NUMERIC" }
        ],
        "confirmAction": {
          "label": "Looks good",
          "actionType": "GUIDED_COMPLETION",
          "uiAction": { "uiActionType": "SUBMIT" },
          "guidanceMarker": "emergency_fund:profile:confirm"
        },
        "editAction": {
          "label": "I'd like to change something",
          "actionType": "STANDARD_COMPLETION",
          "uiAction": { "uiActionType": "FOCUS_CHAT_INPUT" }
        }
      }
    }
  ]
}
```

### A.5 Spouse Entity Input

**uiIntent input:**
```json
{
  "message": "What's Priya's monthly take-home income?",
  "collect": {
    "fields": [{
      "fieldRef": "monthlyNetIncome",
      "entityGroupId": "person_2"
    }]
  }
}
```

**Resolver output (assuming `entitySummaries.person_2 = { firstName: "Priya", relationshipToUser: "SPOUSE" }`):**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "What's Priya's monthly take-home income?" }
    },
    {
      "componentId": "VALUE_INPUT",
      "entityMeta": { "entityGroupId": "person_2", "relationshipToUser": "SPOUSE" },
      "data": {
        "fieldKey": "monthlyNetIncome",
        "title": "Priya's Monthly In-Hand Income",
        "inputType": "SLIDER",
        "config": {
          "unit": "INR", "period": "MONTHLY",
          "min": 0, "max": 50000000, "step": 1000,
          "smartDefault": 50000
        },
        "submitAction": { "label": "Continue", "actionType": "GUIDED_COMPLETION", "uiAction": { "uiActionType": "SUBMIT" } }
      }
    }
  ]
}
```

### A.6 Multi-Field Form (Expenses)

**uiIntent input:**
```json
{
  "message": "Let's get a picture of your monthly outflows.",
  "collect": {
    "formTitle": "Monthly Expenses",
    "fields": [
      { "fieldRef": "monthlyRent", "entityGroupId": "person_1", "description": "Your rent or housing loan EMI." },
      { "fieldRef": "monthlyNonDiscretionaryExpenses", "entityGroupId": "person_1", "description": "Groceries, utilities, transport — essentials you can't easily cut.", "exclusions": "Don't include rent or EMIs." },
      { "fieldRef": "totalMonthlyEmi", "entityGroupId": "person_1", "description": "All loan EMIs combined — home, car, personal." }
    ]
  }
}
```

**Resolver output:**
```json
{
  "components": [
    {
      "componentId": "RESPONSE_TEXT",
      "data": { "text": "Let's get a picture of your monthly outflows." }
    },
    {
      "componentId": "FORM_GROUP",
      "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
      "data": {
        "title": "Monthly Expenses",
        "showTotal": true,
        "totalLabel": "Total Monthly",
        "components": [
          {
            "componentId": "VALUE_INPUT",
            "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
            "data": {
              "fieldKey": "monthlyRent", "title": "Monthly Rent",
              "description": "Your rent or housing loan EMI.",
              "inputType": "SLIDER",
              "config": { "unit": "INR", "period": "MONTHLY", "min": 0, "max": 500000, "step": 500, "smartDefault": 15000 },
              "submitAction": null
            }
          },
          {
            "componentId": "VALUE_INPUT",
            "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
            "data": {
              "fieldKey": "monthlyNonDiscretionaryExpenses", "title": "Monthly Essential Expenses",
              "description": "Groceries, utilities, transport — essentials you can't easily cut.",
              "exclusions": "Don't include rent or EMIs.",
              "inputType": "SLIDER",
              "config": { "unit": "INR", "period": "MONTHLY", "min": 5000, "max": 10000000, "step": 1000, "smartDefault": 25000 },
              "submitAction": null
            }
          },
          {
            "componentId": "VALUE_INPUT",
            "entityMeta": { "entityGroupId": "person_1", "relationshipToUser": "SELF" },
            "data": {
              "fieldKey": "totalMonthlyEmi", "title": "Total Monthly EMI",
              "description": "All loan EMIs combined — home, car, personal.",
              "inputType": "SLIDER",
              "config": { "unit": "INR", "period": "MONTHLY", "min": 0, "max": 10000000, "step": 1000, "smartDefault": 0 },
              "submitAction": null
            }
          }
        ],
        "submitAction": {
          "label": "Continue",
          "actionType": "GUIDED_COMPLETION",
          "uiAction": { "uiActionType": "SUBMIT" }
        }
      }
    }
  ]
}
```

---

## Appendix B: Palette Component Required Fields Reference

Quick reference of `required` arrays from [component-palette-openapi-v2.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/strategy/buildspecs/component-palette-openapi-v2.json):

| Component | Envelope required | Data required |
|---|---|---|
| `RESPONSE_TEXT` | `componentId`, `data` | `text` |
| `VALUE_INPUT` | `componentId`, `data` | `fieldKey`, `inputType`, `submitAction` |
| `TIERED_SELECTOR` | `componentId`, `data` | `fieldKey`, `items`, `submitAction` |
| `FORM_GROUP` | `componentId`, `data` | `title`, `components`, `submitAction` |
| `REVIEW_CARD` | `componentId`, `data` | `title` |
| `ALLOCATION_BAR` | `componentId`, `data` | `title`, `layers`, `primaryAction` |
| `MILESTONE_PLAN` | `componentId`, `data` | `title`, `headline`, `milestoneHeader`, `milestones`, `primaryAction` |

---

## Appendix C: inputType Exhaustive Cross-Reference

Every `inputType` value in `profile-fields-schema-v2.json` and which fields use it:

| `inputType` | Fields | Component → Mode |
|---|---|---|
| `CURRENCY` | `monthlyNetIncome`, `annualGrossIncome`, `monthlyRent`, `monthlyNonDiscretionaryExpenses`, `totalMonthlyEmi`, `totalOutstandingDebt`, `monthlySipCommitment`, `totalLiquidSavings`, `totalEquityInvestments`, `totalFixedIncome`, `healthInsuranceCoverAmount`, `termLifeCoverAmount`, `section80cUtilized` | `VALUE_INPUT` → `SLIDER` |
| `NUMBER` | `targetRetirementAge`, `childrenUnder5Count`, `childrenSchoolAgeCount`, `agingParentInsuredCount`, `agingParentUninsuredCount`, `otherDependentCount`, `numberOfDependents` | `VALUE_INPUT` → `SLIDER` |
| `TEXT` | `preferredName`, `cityName` | `VALUE_INPUT` → `TEXT` |
| `DATE` | `dateOfBirth` | `VALUE_INPUT` → `TEXT` |
| `BOOLEAN` | `primaryEarner` | `TIERED_SELECTOR` → chips |
| `CHOICE_SIMPLE` | `gender`, `maritalStatus`, `employmentType`, `incomePattern`, `dependencyStatus`, `decisionMakingStyle`, `residenceStatus`, `healthInsuranceSource`, `householdIncomeType`, `cityTier`, `tobaccoStatus` | `TIERED_SELECTOR` → chips |
| `CHOICE_TIERED` | `employerStabilityTier`, `preExistingConditions`, `incomeTaxRegime`, `dependentHealthInsuranceStatus`, `riskTolerance`, `financialLiteracyLevel` | `TIERED_SELECTOR` → cards |

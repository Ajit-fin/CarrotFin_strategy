# Unified Field Model Specification

This document provides a type-level specification for the Unified Field Model, organized into core entities and logically grouped enumerations.

## Core Entities

### `FieldDefinition`
The central model defining a field's structure, metadata, constraints, and UI representation.

**Properties:**
- `key` (String)
- `origin` (`Origin`)
- `scope` (`Scope`)
- `conversationId` (String)
- `dataType` (`FieldDataType`)
- `unit` (`Unit`)
- `period` (`Period`)
- `shortLabel` (String)
- `descriptiveLabel` (String)
- `description` (String)
- `infotip` (String)
- `category` (`Category`)
- `min` (BigDecimal)
- `max` (BigDecimal)
- `step` (BigDecimal)
- `smartDefault` (BigDecimal)
- `minLength` (Integer)
- `maxLength` (Integer)
- `minDate` (String)
- `maxDate` (String)
- `options` (List<`Option`>)
- `uiRepresentationType` (`UiRepresentationType`)
- `applicabilityContext` (`ApplicabilityContext`)
- `journeyTags` (List<`JourneyTag`>)
- `derivation` (`Derivation`)
- `invalidates` (List<String>)

**Methods:**
- `isNumeric()`: boolean
- `skipOption()`: Optional<`Option`>
- `selectableOptions()`: List<`Option`>

### `Option`
Defines a selectable option for a field.

**Properties:**
- `value` (String)
- `label` (String)
- `description` (String)
- `infotip` (String)
- `icon` (String)

### `ApplicabilityContext`
Defines the rules under which a field is applicable.

**Properties:**
- `rule` (String)

### `Derivation`
Defines how a field's value is derived from other fields.

**Properties:**
- `type` (`DerivationType`)
- `applicableWhenEntity` (`Relationship`)
- `from` (List<String>)
- `logic` (String)

---

## Enumerations

### Field Configuration & Metadata
- **`Origin`**: `CORE`, `JIT`, `CANDIDATE`
- **`Scope`**: `GLOBAL`, `PLAN`, `SCENARIO`
- **`FieldDataType`**: `TEXT`, `INTEGER`, `LONG`, `FLOAT`, `BOOLEAN`, `DATE`
- **`Category`**: `PERSONAL`, `INCOME`, `HEALTH`, `INSURANCE`, `BEHAVIORAL`, `EXPENSE`, `DEBT`, `ASSET`, `TAX`, `DEMOGRAPHICS`
- **`JourneyTag`**: `ALL`, `TAX`, `INVESTMENT`, `EMERGENCY_FUND`, `GOALS`, `INSURANCE`

### Measurement & Time
- **`Unit`**: `INR`, `PERCENT`, `YEARS`, `MONTHS`, `DAYS`, `COUNT`
- **`Period`**: `MONTHLY`, `QUARTERLY`, `ANNUAL`, `ONE_TIME`

### UI & Presentation
- **`UiRepresentationType`**: `SLIDER`, `TEXT_INPUT`, `CHIPS`, `CARDS`

### Field Computation
- **`DerivationType`**: `SYSTEM_DERIVED`, `SUGGEST_AND_CONFIRM`

### Data Traceability
- **`Provenance`**: `EXTRACTED`, `FORM_SUBMITTED`, `COMPUTED`, `INFERRED`
- **`Confidence`**: `HIGH`, `MEDIUM`, `LOW`

### Domain Relationships
- **`Relationship`**: `SELF`, `SPOUSE`, `PARENT`, `CHILD`, `HOUSEHOLD`, `SIBLING`, `OTHER`

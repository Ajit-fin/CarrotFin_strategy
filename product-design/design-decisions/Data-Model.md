# Data Model — Consolidated Design Decisions

> **Consolidates:** DD10, DD11, DD12  
> **Date range:** 2026-07-06 to 2026-07-21  
> **Last updated:** 2026-07-29  
> **Canonical schema:** [profile-fields-schema.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/profile-fields-schema.json)

---

## DD10: Profile Fields vs. Planning Variables

**Decision:** All user data splits into two categories — **Profile Fields** (curated, persisted, globally scoped facts = "the Client File") and **Planning Variables** (ephemeral, journey-scoped inputs consumed by the LLM = "the Workpad"). The asymmetric naming is intentional: Fields are schematized columns; Variables are flexible JSON in session state.

**Rationale:** Creates unambiguous rules for data persistence, extraction routing, and analytics queryability. Profile Fields are structured and queryable; Planning Variables are best-effort.

**Consequences:**
- Any data collected as a Profile Field writes to DB immediately and is never re-asked.
- Planning Variables stay in journey thread state and are always re-derived per session.
- A deliberate "promotion path" exists: a recurring Planning Variable can be elevated to a Profile Field with schema + backfill.

---

## DD11: Profile Fields Library — Four Entity Types

**Decision:** Profile Fields are structured across four entity types: **Person (A)** — atomic individual facts (income, age, insurance); **Household (B)** — collective financial unit (expenses, savings, dependents); **Relationship (C)** — edges between persons (dependency, decision-making, cross-coverage); **Goal (D)** — active financial objectives (deferred to V2).

**Schema:** Full field inventory in [profile-fields-schema.json](file:///Users/kshekhaw/Documents/CarrotFin_strategy/product-design/profile-fields-schema.json). MVP uses flattened structure, CAPS_CASE enums, camelCase keys for direct LLM prompt injection.

**Note:** `numberOfDependents` deprecated → 5 granular count fields (`childrenUnder5Count`, `childrenSchoolAgeCount`, `agingParentInsuredCount`, `agingParentUninsuredCount`, `otherDependentCount`). Health insurance for dependents stored as Relationship link-property, not Person property. LifeEvent log deferred to V2.

---

## DD12: Individual → Group/Family Profile Model

**Decision:** The household (GroupProfile) is the atomic data unit, not the individual. All buildspecs accept `GroupProfile` as input context, modeled across DD11's four entity types.

**Rationale:** Indian personal finance is inherently household-level — income pooling, shared expenses, cross-entity insurance coverage, and joint decision-making are the norm. Individual-only modeling produces advisory blind spots.

**Assumptions:** GP-01

**Reversal trigger:** User testing reveals Indian users strongly resist providing family data, or entity resolution accuracy is too low for V1.

---

## Considered & Rejected

- **DD10 — "Core Fields / JIT Fields" naming:** Replaced by Profile Fields / Planning Variables for clearer architectural semantics.
- **DD12 — Option A (Individual-only profile):** Simpler schema but cannot model shared expenses, dual-income hedging, or cross-entity insurance — advisory quality capped.

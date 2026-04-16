# DD08: Three-Layer Allocation Visual — Horizontal Liquidity Gradient Strip

> **Date:** 2026-04-16  
> **Tension resolved:** T1 (Simplicity vs. Financial Rigor — partially, for Phase 4)  
> **Open question resolved:** Q4 (3-layer allocation visual)  
> **Assumptions depended on:** EF-11 (8 core sizing dimensions), C5 (conversational+visual integration feasible)

## Context

Phase 4 (Fund Architecture) must visually communicate the 3-layer liquidity architecture from philosophy §5. Open question Q4 asks: bar chart, layered area chart, or conceptual illustration? The constraint: the visual must communicate liquidity as a spectrum, not three separate buckets.

## Options Evaluated

| Option | Pros | Cons | Risk |
|---|---|---|---|
| **Bar chart (three separate bars)** | Familiar, easy to read | Communicates "buckets" — three distinct, separate containers. Fails the spectrum requirement. | Users think layers are independent, not a continuum. |
| **Other visual forms (area chart, stacked illustration)** | — | Communicate buckets not spectrum, or add cognitive overhead (axes, legends). | Fail either the simplicity or the spectrum requirement. |
| **Horizontal liquidity gradient strip** | Single visual element. Left-to-right is universal. Gradient communicates spectrum. Zone widths proportional to ₹ amounts. | Novel pattern — no established user mental model. | Mitigated by the AI's "three speeds" framing which primes the mental model before the visual appears. |

## Decision

**Horizontal liquidity gradient strip.** A single continuous horizontal bar with three colour-coded zones flowing left (warm amber — Instant Access) to right (cool blue-slate — Stability Reserve). Each zone is proportionally sized by ₹ amount and labeled with layer name, amount, vehicle type, and access time. Below the strip, three detail rows expand on each layer.

**Why this resolves T1 for Phase 4:**
- **Simplicity:** One visual element (a bar). Left-to-right reading is universal. Colours (warm = accessible, cool = locked) are intuitive.
- **Financial rigor:** Three distinct layers with specific amounts, vehicle types, and access times. The spectrum framing is financially accurate — liquidity IS a continuous property.
- **Sequenced comprehension:** The "three speeds" conversational framing (Step 4.2) primes the user's mental model before the visual materializes. The strip confirms what the user has already understood.

## Consequences

- Phase 4's Allocation Card uses the strip as its anchor visual, with detail rows below.
- Flutter implementation needs proportional zone rendering — variable widths based on ₹ amounts.
- The gradient colour scheme (warm → cool) becomes a reusable idiom for "liquidity spectrum" across the app.
- The advisory ceiling (DD02/D4) is respected — the strip shows instrument TYPES, not specific products.

## Reversal Trigger

- User testing shows >30% misinterpret the strip's direction — fall back to labeled vertical blocks (stacked, top = most liquid).
- If zone widths compress below readable thresholds on mobile for small allocations, consider a vertical stacked variant.

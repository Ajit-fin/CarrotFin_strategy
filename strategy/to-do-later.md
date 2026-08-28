# To-Do Later

This file serves as a reminder list for checks, verifications, and technical debt items that need to be addressed soon, but are out-of-scope for the active working thread. 

*(Note: Use `ideas-backlog.md` for future product and feature improvements. Use this file for tactical checks and syncs.)*

## Active Checks
- [ ] **Context Injection Sync:** Check actual production backend — what exactly is being injected into `contextInjectionSpec` in every prompt? Is `userProfile` data fully implemented and flowing from the backend today?

## Extraction Prompt — Deferred Audit Items [2026-07-31]
Source: `flash_extraction_v2.xml` consistency review.

- [ ] **clauseSegment coverage gap:** Architecture says every clauseSegment must produce an entry in mappedFields/unmappedFields/modifiers. But Example 1's userQuery has "help me calculate my emergency fund" which produces zero segments. Decide: should intent phrases be segmented, or amend the rule?
- [ ] **Child ID ordering convention:** ID assignment says "order of first mention." Example 1 assigns CHILD_1 to the 3-year-old (first number mentioned). Consistent, but LLMs may default to eldest=1. Consider whether to add a clarifying note or leave as-is.
- [ ] **Age heuristic ambiguity:** Heuristic maps "I am X years old" → dateOfBirth. But children's age maps to generic key "age" (Example 1). Heuristic doesn't explicitly distinguish user's own age vs family members. Consider clarifying.
- [ ] **Intent phrase not captured:** "help me calculate my emergency fund" is dropped from clauseSegments entirely. The goal context is only inferred from mapped field keys. Consider adding as a segment or noting it as acceptable.
- [ ] **Schema enum gaps — fieldType:** Examples use CURRENCY, CHOICE, NUMBER, TEXT, DATE. No enum constraint in responseSchema. Consider adding enum to prevent model drift.
- [ ] **Schema enum gaps — period:** Examples use MONTHLY, ANNUAL. No enum constraint in responseSchema.
- [ ] **Schema enum gaps — qualifier:** Example uses APPROXIMATE. No enum constraint in responseSchema.


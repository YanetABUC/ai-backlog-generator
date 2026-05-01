Rewrite a backlog item to fix evaluation issues, update the file in place, and refresh the timestamp.

---

## Handling Input

The user provides an item ID (`US-001`, `BUG-003`) and optionally evaluation feedback.

If only an ID is given:
1. Read `backlog/counter.json` to find the file path
2. Read the item file
3. Check frontmatter for `reports.evaluation` — if a report exists, read it to get the specific issues
4. If no report exists, ask: "What issues should I fix? (Or run `/backlog:evaluate-item {ID}` first.)"

If the user provides inline feedback text, use that as the issues list.

---

## Iteration Limit

Cap at **two refinement passes per item**. If after two passes a User Story is still below 7.0, tell the user:

> "This item needs to be regenerated with richer context — a more specific persona, better domain model, or clearer business rules. Use `/backlog:generate-items` instead of refining further."

---

## Refinement Rules by Type

### User Story (US)

Apply every rule relevant to the identified issues:

1. Preserve core intent — do not add scope not implied
2. Must start with "In order to" — a specific business goal
3. Persona must be role + context (never "user" or "admin")
4. "So that" must be a measurable user benefit
5. Collapse any separate Business Objective / Problem Context / Desired Outcome sections into one Context paragraph (2–5 sentences)
6. Every AC must be a numbered, declarative pass/fail statement — no Given/When/Then in AC
7. BDD Scenarios are optional — include only when AC cannot express a complex multi-step interaction (max 5)
8. Remove any Functional Requirements section — move observable behaviors to AC; move audit/logging to NFRs
9. Remove shape prescription anywhere: return type enumerations, result object structures, function signatures — rewrite as observable behavior
10. NFRs must be project-specific with measurable thresholds
11. Remove prohibited sections: Implementation Notes, Technical Design, Pseudocode, file paths, API specs
12. Target 80–150 lines — flag if still over 200 after refinement

### Bug (BUG)

Fix the specific failing gates:
- If steps are incomplete: ask the user for the missing step detail
- If actual behavior lacks exact error text: ask the user for the message
- If severity is unjustified: add a one-sentence rationale
- If AC for the fix are vague: rewrite as declarative pass/fail statements

### Spike (SPK)

- If the investigation question is vague: sharpen it to a single answerable question
- If the timebox is missing: ask the user for the number of days
- If the expected output is vague: rewrite as a concrete artifact ("a written recommendation on X" not "we'll know more")

### Task (TSK)

- If description is unclear: ask for clarification before rewriting
- If AC are vague: rewrite as observable conditions

---

## After Rewriting

1. Update `updated_at` in frontmatter to current ISO timestamp
2. Write the updated content back to the same file (do not move it)
3. Tell the user: "Refined in place. Run `/backlog:evaluate-item {ID}` to confirm the grade improved."
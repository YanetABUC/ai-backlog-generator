Rapid quality audit across a set of backlog items. Reads items from the backlog folder or by ID list, classifies each by readiness, and gives a prioritized fix order.

---

## Handling Input

The user may provide:
- A list of IDs: `US-001 US-002 BUG-003 US-004`
- A folder scope: `draft`, `in-review`, or `all`
- No input: audit everything in `backlog/backlog-items/` (all subfolders)

**Resolving items:**
1. Read `backlog/counter.json`
2. For each ID in the list, find the path and read the file
3. For folder scope: filter the index by `status` field and read all matching files

---

## Rapid Quality Gates

### User Stories — 14 Gates (pass/fail)

1. Starts with "In order to" — not "As a" or a noun title
2. "In order to" is a business goal — not a feature description
3. Persona is role + context — not "user," "admin," or "customer" alone
4. "So that" is a measurable user benefit
5. AC are declarative pass/fail statements — no Given/When/Then in AC
6. AC are not vague ("system should work" fails)
7. All four BDD scenario types present: Happy Path, Validation, Edge Case, Failure Handling
8. At least one error state is defined
9. Story fits in a sprint — 3 days max
10. No Functional Requirements section
11. No separate Business Objective / Problem Context / Desired Outcome sections
12. No shape prescription anywhere
13. NFRs have measurable thresholds
14. Out of Scope is present and explicit

### Bugs — 6 Gates
1. Summary states what is broken and for whom
2. Steps to reproduce are complete
3. Expected and actual behavior both defined with exact error text
4. Severity is set and justified
5. AC for the fix are declarative
6. Out of Scope is explicit

### Spikes — 4 Gates
1. Investigation question is specific and answerable
2. Timebox is defined
3. Expected output is a concrete artifact
4. Definition of Done is verifiable

### Tasks — 3 Gates
1. Description is clear
2. AC define done in observable terms
3. Dependencies are listed

---

## Output Format

### Summary Table

| ID | Type | Gates Failed | Status |
|---|---|---|---|
| US-001 | Story | None | ✅ Dev-Ready |
| US-002 | Story | 5, 7, 12 | ❌ Requires Rework |
| BUG-003 | Bug | 3 | ⚠️ Needs Work |

**Total:** [N] items — [N] ✅ Ready, [N] ⚠️ Needs Work, [N] ❌ Requires Rework

---

### Critical Failures (Fix First)

Items failing 3+ gates or any of gates 1, 3, 4, 5, 7, 12 for stories:

**{ID} — {Title}** ❌
- Gates failed: [list with descriptions]
- Fastest fix: [specific action]
- Recommended action: `/backlog:refine-item {ID}` or return to `/backlog:generate-items`

---

### Targeted Fixes

Items failing 1–2 gates:

**{ID} — {Title}** ⚠️
- Issue: [specific problem]
- Fix: [specific correction]
- Command: `/backlog:refine-item {ID}` / `/backlog:identify-edge-cases {ID}` / `/backlog:split-item {ID}`

---

### Ready Items

✅ {ID} — {Title}

---

### Systemic Issues

Top 3 patterns across this backlog:
1. [Issue] — affects [N] items
2. [Issue] — affects [N] items
3. [Issue] — affects [N] items

**Root cause recommendation:** [one change to the generation process that prevents these upstream]

---

### Prioritized Fix Order

Fix in this sequence for fastest sprint readiness:
1. Items failing gates 5, 7, 12 (AC, BDD, shape prescription) — blocks engineering most
2. Items failing gates 1, 3, 4 (story format) — quick fixes, high clarity impact
3. Items needing splits (gate 9) — split first, then evaluate each piece
4. Items missing Out of Scope or NFR thresholds — lowest risk, fix last

After the audit: "Run `/backlog:sprint-prep` on the ready items to finalize for sprint."
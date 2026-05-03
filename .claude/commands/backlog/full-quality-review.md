Run all three quality layers on a backlog item in one pass — structural evaluation (8-dimension rubric), edge case sweep (adds Critical and High cases to AC automatically), and semantic review (claim traceability, goal-AC alignment, BDD-AC alignment, cross-story consistency, inference audit). Produces a single unified report and one verdict.

---

## Handling Input

The user may provide:
- A local ID: `US-001`
- A Jira link: `https://company.atlassian.net/browse/PROJ-42`
- Pasted content (note which checks will be limited due to missing context)

---

## Step 1: Load All Context

1. Read `backlog/counter.json`, resolve the item path, read the item file
2. Read the parent epic file (from the item's `epic` frontmatter field)
3. Scan `backlog/discovery/` — for each file, check if the `epics` frontmatter includes the parent epic ID. If found, read the full discovery record
4. Read all sibling items in the same epic: query `counter.json` for items sharing the same `epic` value, read each file
5. If `backlog/DoD.md` exists, read it

Load everything before running any check. All three layers share the same context.

---

## Step 2: Structural Evaluation

Apply the full 8-dimension rubric from `evaluate-item.md`.

Score each dimension 1–3. Grade = round(total / 24 × 10, 1).

**1. User Clarity (1–3)**
- 1 = Does not start with "In order to," or business goal is circular
- 2 = "In order to" present but vague; persona lacks context
- 3 = Leads with specific business goal; persona is role + context

**2. Business Value (1–3)**
- 1 = No "so that" or it's circular
- 2 = Value stated but not measurable
- 3 = Measurable outcome tied to real user goal or business metric

**3. AC Quality (1–3)**
- 1 = Vague, missing, or written as Given/When/Then
- 2 = Partially testable — some subjective or compound criteria
- 3 = All declarative pass/fail; independent; no implementation details

**4. BDD Scenarios (1–3)**
- 1 = Missing or only Happy Path
- 2 = Two+ types present but error text unquoted or multiple When per scenario
- 3 = All four types; single When each; error messages quoted; Failure Handling confirms data preserved

**5. Story Size (1–3)**
- 1 = Spans multiple subsystems or exceeds 5 days
- 2 = Borderline — could fit but risky
- 3 = 1–3 days of focused work

**6. Edge Case Coverage (1–3)**
- 1 = Happy path only
- 2 = Happy path + some edge cases
- 3 = Happy path + error state + 2+ edge cases with explicit threshold behavior

**7. Dependency Clarity (1–3)**
- 1 = Dependencies not mentioned
- 2 = Some identified, some unclear
- 3 = All explicitly listed (APIs, design assets, other items)

**8. Conciseness / NFRs / Out of Scope (1–3)**
- 1 = Separate Business Objective / Problem Context sections; or FR section; or shape prescription; or 200+ lines
- 2 = Context one paragraph but verbose; NFRs present but some boilerplate; Out of Scope thin
- 3 = Under 150 lines; one concise Context paragraph; no FR section; no shape prescription; measurable NFR thresholds; explicit Out of Scope

**DoD compliance:** If `backlog/DoD.md` exists, check Required AC Coverage and Required NFR Standards. List any gaps separately — they do not affect the grade but must be resolved before handoff.

Record the scorecard and grade. Continue to Step 3 regardless of grade — run all three layers before routing.

---

## Step 3: Edge Case Sweep

Run a full sweep across all six categories against the item content loaded in Step 1.

**Data:** empty/null input, maximum length, special characters, numbers at 0 or negative, boundary dates

**Permissions & roles:** role changes mid-session, feature access without record-level access, partial permissions, cross-user resource access

**State & timing:** record state changes between load and submit, concurrent modification, double-submit, interrupted multi-step flow

**System & integration:** downstream API timeout, unexpected error response, DB write failure after API success, third-party service unavailable

**Volume & scale:** 0, 1, or 1,000+ items in a list, empty vs. whitespace-only input, search with no results

**Browser & device (if UI):** mobile with slow connection, browser back mid-flow, two tabs on same session, session expiry mid-flow

Only include edge cases that are genuinely possible given this feature's context.

After generating the edge cases, pause and ask:
> "Found [N] Critical/High severity edge cases. Should I add them to the AC in {ID} before continuing with the semantic review?"

If yes: append them to the AC section, update `updated_at` in frontmatter, write the file, then continue to Step 4.
If no: include them in the report as findings only and continue to Step 4 without modifying the file.

If Edge Case Coverage was scored 1 or 2 in Step 2 and the user added the cases, note the corrected dimension score in the report.

---

## Step 4: Semantic Review

Run all five semantic checks using the context loaded in Step 1.

**Check 1 — Claim Traceability**
For each AC item (including any added in Step 3), determine if the claimed behavior traces to the discovery record, the epic's functional expectations, or a validated assumption. Flag any AC item with no traceable source.

**Check 2 — Goal-AC Alignment**
Test necessity: does every AC item contribute to the "so that" goal? Test sufficiency: if all ACs pass, is the goal actually achieved? Flag gaps in both directions.

**Check 3 — BDD-AC Alignment**
Flag orphaned BDD scenarios (test behavior not in any AC) and untested ACs (no corresponding scenario). Re-check against any AC items added in Step 3.

**Check 4 — Cross-Story Consistency**
Compare against sibling stories. Flag contradictions in persona descriptions, behavioral rules, thresholds, and domain term definitions.

**Check 5 — Inference Audit**
Identify claims embedded as facts that are deductions rather than stated requirements: behavioral preferences, outcome predictions, invented default-state behaviors, scope assumptions.

Classify each finding as High / Medium / Low.

---

## Step 5: Produce the Unified Report

```
# Full Quality Review — {ID}: {Title}
Date: {YYYY-MM-DD}

---

## Layer 1: Structural Evaluation

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | /3 | |
| Business Value | /3 | |
| AC Quality | /3 | |
| BDD Scenarios | /3 | |
| Story Size | /3 | |
| Edge Cases | /3 → /3* | *updated after edge case sweep |
| Dependencies | /3 | |
| Conciseness / NFR / OoS | /3 | |
| **Total** | **/24** | |
| **Grade** | **/10** | |

**Status:** ✅ Dev-Ready (≥9.0) / ⚠️ Needs Refinement (7.0–8.9) / ❌ Requires Rework (<7.0)

[DoD gaps if any — listed separately, do not affect grade]

---

## Layer 2: Edge Case Sweep

[N] edge cases found. [N] Critical/High added to item AC.

**Added to AC:**
- EC-{N}: {name} — {severity}
- ...

**Low severity (noted, not added):**
- EC-{N}: {name} — {scenario in one sentence}
- ...

[If no gaps found: "Edge case coverage is complete — no additions required."]

---

## Layer 3: Semantic Risk Register

**Context loaded:** Epic {EP-ID} | Discovery: {filename or "none found"} | Siblings: {N} items

### High Risk — Resolve Before Handoff
[SR-H{N}] {label}
- Found in: [location]
- Issue: [description]
- Verify: [question]

### Medium Risk — Confirm Before Sprint
[SR-M{N}] {label}
- Found in: [location]
- Issue: [description]
- Verify: [question]

### Low Risk — Worth Noting
[SR-L{N}] {label}
- Found in: [location]
- Issue: [description]

### Checks Passed
- [Check name]: no issues found

---

## Questions to Verify

Numbered list of every Verify question from all High and Medium risks:
1. [question]
2. [question]
...

---

## Verdict

| Layer | Result |
|---|---|
| Structural | [Grade /10 — status] |
| Edge Cases | [[N] added to AC / complete] |
| Semantic | [No High risks / [N] High risks] |

**Overall:** [one of the four below]

✅ **Proceed to handoff** — Structural ≥9.0, edge cases complete, no High semantic risks.
Run `/backlog:dev-ready-handoff {ID}`.

⚠️ **Resolve semantic risks first** — Structural passes but [N] High semantic risks found.
Resolve the questions above, update the item file, re-run `/backlog:semantic-review {ID}`, then run `/backlog:dev-ready-handoff {ID}`.

🔧 **Fix structural issues first** — Grade [X]/10. Run `/backlog:refine-item {ID}`.
After refinement re-run `/backlog:full-quality-review {ID}`. Cap: max 2 refinement passes.

❌ **Requires rework** — Grade below 7.0. Return to `/backlog:generate-items` with richer context.
This is a context problem, not a wording problem.
```

---

## Step 6: Save Report and Update Item

1. Write the unified report to `backlog/reports/{ID}-full-review-{YYYY-MM-DD}.md`
2. Update the item's frontmatter:
   - `status`: change to `in-review`
   - `updated_at`: current ISO timestamp
   - `reports.full_quality_review`: path to the report
3. Move the item file to `backlog/backlog-items/in-review/`
4. Update `backlog/counter.json`: change `path` and `status` to `in-review`

The item file already has the new edge case AC items appended from Step 3. Write the final state of the item file before moving it.

---

## Step 7: Confirm

Tell the user:
- Structural grade
- How many edge cases were added
- How many semantic High risks were found (if any)
- The overall verdict and the exact next command to run

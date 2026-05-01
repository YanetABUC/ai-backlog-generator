Score a backlog item against the appropriate quality rubric, save the evaluation report, link it in the item file, and move the item to in-review.

---

## Handling Input

The user may provide:
- A local ID: `US-001`, `BUG-003`, `EP-001`
- A Jira link: `https://company.atlassian.net/browse/PROJ-42`
- Pasted content directly (no file operations in this case)

## Step 1: Resolve the Item

**If local ID provided:**
1. Read `backlog/counter.json`
2. Find the entry for the given ID
3. Read the file at the path in the index

**If Jira link provided:**
1. Extract the issue key (e.g. `PROJ-42`)
2. Read `backlog/counter.json`
3. Search the index for an entry where `jira_key` matches the extracted key
4. Read the file at that path

**If content pasted:**
Evaluate directly — skip all file operations.

---

## Step 2: Evaluate by Item Type

Read the `type` field from the file's frontmatter.

---

### User Story (US) — 8-Dimension Rubric

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

**Output:**
| Dimension | Score | Issue |
|---|---|---|
| User Clarity | /3 | |
| Business Value | /3 | |
| AC Quality | /3 | |
| BDD Scenarios | /3 | |
| Story Size | /3 | |
| Edge Cases | /3 | |
| Dependencies | /3 | |
| Conciseness / NFR / OoS | /3 | |
| **Total** | **/24** | |
| **Grade** | **/10** | |

**Status:** ✅ Dev-Ready (≥9.0) / ⚠️ Needs Refinement (7.0–8.9) / ❌ Requires Rework (<7.0)

**Top 2 fixes:** [specific and actionable]

---

### Bug (BUG) — 6-Gate Rubric

Score each gate Pass / Fail.

1. Summary is one sentence stating what is broken and for whom
2. Steps to reproduce are complete — a developer can reproduce without asking questions
3. Expected and actual behavior are both defined; actual includes exact error message text
4. Severity is set and justified
5. Acceptance Criteria for the fix are declarative and verifiable
6. Out of Scope explicitly lists what the fix does not address

**Status:** ✅ Ready (all gates pass) / ❌ Incomplete (list failing gates with specific fix)

---

### Spike (SPK) — 4-Gate Rubric

1. Investigation question is specific and answerable within the timebox
2. Timebox is defined and realistic
3. Expected output is a concrete artifact or decision (not "we'll know more")
4. Definition of Done is verifiable — someone can tell the spike is finished without asking

**Status:** ✅ Ready / ❌ Incomplete (list failing gates)

---

### Task (TSK) — 3-Gate Rubric

1. Description clearly states what needs to be done and why
2. Acceptance Criteria define done in observable terms
3. Dependencies are listed

**Status:** ✅ Ready / ❌ Incomplete

---

## Step 3: Save Report

Write the report to `backlog/reports/{ID}-eval-{YYYY-MM-DD}.md` containing the full evaluation output.

---

## Step 4: Update Item File

Update the item's frontmatter:
- `status`: change to `in-review`
- `updated_at`: current ISO timestamp
- `reports.evaluation`: path to the report just written (e.g. `backlog/reports/{ID}-eval-{YYYY-MM-DD}.md`)

---

## Step 5: Move File and Update Counter

1. Move the file from its current folder to `backlog/backlog-items/in-review/`
2. Update `backlog/counter.json`:
   - Change `path` for this ID to the new location
   - Change `status` to `in-review`

---

## Step 6: Confirm and Route

Tell the user the grade and status, then route:

- **≥ 9.0 (User Story):** "Ready to proceed. Run `/backlog:dev-ready-handoff {ID}` for the final pre-sprint check."
- **7.0–8.9:** "Run `/backlog:refine-item {ID}` — paste the evaluation feedback when prompted."
- **< 7.0:** "Requires rework. Return to `/backlog:generate-items` with richer context — this is a context problem, not a wording problem. Cap: do not refine a story more than twice."
- **Bug/Spike/Task pass:** "Run `/backlog:dev-ready-handoff {ID}`."
- **Bug/Spike/Task fail:** "Fix the flagged gates and re-run `/backlog:evaluate-item {ID}`."
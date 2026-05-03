Surface semantic risks in a backlog item: untraceable claims, goal-AC gaps, BDD-AC misalignment, cross-story contradictions, and unvalidated inferences. Produces a risk register the PM/BA must resolve before handoff — not a score.

---

## When to Run

After `/backlog:evaluate-item` passes (≥7.0) and before `/backlog:dev-ready-handoff`. Structural evaluation checks form. Semantic review checks meaning.

---

## Handling Input

The user may provide:
- A local ID: `US-001`
- A Jira link: `https://company.atlassian.net/browse/PROJ-42`
- Pasted content directly (limited context — note what cannot be checked)

---

## Step 1: Load All Context

**If local ID or Jira link provided:**

1. Read `backlog/counter.json`, resolve the item path, read the item file
2. Read the parent epic file (from the item's `epic` frontmatter field)
3. Scan `backlog/discovery/` — for each file, check if the `epics` frontmatter includes the parent epic ID. If found, read the full discovery record
4. Read all sibling items in the same epic: query `counter.json` for items sharing the same `epic` value, read each file
5. If `backlog/DoD.md` exists, read it

**If content pasted:**
Work with what is provided. Note at the top of the output which checks were skipped due to missing context (epic, discovery, siblings).

**Never ask the user to paste content that exists in a file.**

---

## Step 2: Run Five Semantic Checks

### Check 1 — Claim Traceability

For each Acceptance Criteria item, determine whether the claimed behavior is traceable to one of:
- A problem, user pain, or goal stated in the discovery record
- A functional expectation listed in the parent epic
- An assumption that was explicitly validated (marked resolved, not `[To validate]`)

**Flag as untraceable** any AC item that introduces a behavioral claim with no source in any of the above. Untraceable claims are inferences made during writing — they may be correct, but they are unconfirmed.

Example of a traceable AC:
> "A manager can approve suggestions valued under $5,000 without a secondary confirmation step"
> — traces to Epic Section 5: "Managers at single-location sites have autonomous approval authority up to the daily order limit"

Example of an untraceable AC:
> "Approved suggestions are sorted by vendor name by default"
> — no discovery record, epic section, or validated assumption states this sorting preference. This is an inference.

---

### Check 2 — Goal-AC Alignment

Read the "so that" clause. It states the user goal this story must deliver.

**Test necessity:** Is every AC item required to achieve that goal? Flag any AC item that does not contribute to the "so that" outcome — these are scope creep or misaligned additions.

**Test sufficiency:** If every AC item passes, would the user goal actually be achieved? Identify any condition required to fulfill the "so that" that is not covered by any AC item.

A story where all ACs pass but the goal is not achieved is the most dangerous type of quality failure — it ships without delivering value.

---

### Check 3 — BDD-AC Alignment

For each BDD scenario, identify which AC item it exercises. Then check in both directions:

**Orphaned scenarios:** Flag any BDD scenario that tests behavior not stated in any AC item. These scenarios are testing something outside the story's stated scope.

**Untested ACs:** Flag any AC item that has no corresponding BDD scenario. If a condition is required but never tested, it cannot be verified during QA.

This check catches AC and BDD written independently — a common pattern when they are generated in separate passes.

---

### Check 4 — Cross-Story Consistency

Compare the item under review against its sibling stories (same epic). Check for:

- **Persona contradictions:** Does this story describe the user's role, permissions, or context differently from other stories in the same epic?
- **Behavioral contradictions:** Does any AC item state a rule or threshold that conflicts with a rule or threshold stated in a sibling story? (e.g., US-001 approves up to $5,000; US-003 implies unlimited approval authority)
- **Domain term inconsistency:** Is a business term used with a different meaning across stories? (e.g., "pending" means different states in different stories)

Flag each contradiction with the specific sibling ID and the conflicting statements.

---

### Check 5 — Inference Audit

Read the full story — narrative, AC, BDD scenarios, and NFRs — and identify any claim that is a deduction rather than a stated requirement. These appear as:

- Behavioral preferences not backed by user research ("users will expect to see X")
- Outcome predictions not from validated data ("this will reduce Y by Z%")
- Technical assumptions stated as requirements ("since the API already supports X, we should Y")
- Scope assumptions drawn from adjacent context ("given that feature A exists, feature B must work this way")
- Missing-state behaviors invented without a stated rule ("if no results exist, show an empty state with a prompt" — is this in the discovery record or an inference?)

Flag each inference with the exact sentence, why it appears to be inferred rather than stated, and what source would validate it.

---

## Step 3: Produce the Risk Register

### Output Format

```
## Semantic Review — {ID}: {Title}

**Reviewed against:** Epic {EP-ID} | Discovery: {filename or "none found"} | Siblings: {count} items loaded

---

### High Risk — Resolve Before Handoff

These issues will likely cause rework in sprint if not resolved.

**[SR-H1] {Short label}**
- Found in: [AC item / BDD scenario / NFR / narrative]
- Issue: [what the claim says and why it is risky]
- Verify: [the exact question a PM/BA must answer to resolve this]

...

---

### Medium Risk — Confirm Before Sprint

These issues may cause misalignment during development.

**[SR-M1] {Short label}**
- Found in: [location]
- Issue: [description]
- Verify: [question]

...

---

### Low Risk — Worth Noting

These are observations that may not block the sprint but should be on record.

**[SR-L1] {Short label}**
- Found in: [location]
- Issue: [description]
- Verify: [question]

---

### Checks Passed

- [Check name]: No issues found
- ...

---

### Questions to Verify

Numbered list of every question from all risk items — formatted for copy-paste into a stakeholder review or team sync:

1. [Question from SR-H1]
2. [Question from SR-H2]
...

---

### Verdict

**Proceed to handoff:** [Yes — no High risks] / [No — resolve High risks first]

After resolving all High risks, update the item file and run `/backlog:dev-ready-handoff {ID}`.
```

---

## Step 4: Save Report and Update Item

1. Write the full report to `backlog/reports/{ID}-semantic-{YYYY-MM-DD}.md`
2. Update the item's frontmatter:
   - `updated_at`: current ISO timestamp
   - `reports.semantic_review`: path to the report just written

Do not change item status or move the file — the item remains in its current folder until `/backlog:dev-ready-handoff` is run.

---

## Step 5: Route

- **No High risks:** "Semantic review complete — no blocking issues. Run `/backlog:dev-ready-handoff {ID}` to finalize."
- **High risks found:** "Semantic review found [N] high-risk issues. Resolve the questions in the risk register, update the item file, and re-run `/backlog:semantic-review {ID}`."
- **Medium/Low only:** "No blocking issues. Review the medium-risk items with your team before sprint. Run `/backlog:dev-ready-handoff {ID}` when ready."

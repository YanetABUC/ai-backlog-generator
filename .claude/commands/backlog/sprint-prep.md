Combined sprint preparation: evaluate all items in a set, classify by readiness, run dev-ready checks on passing items, and generate a sprint kickoff note.

---

## Handling Input

The user may provide:
- A list of IDs: `US-001 US-002 BUG-003`
- A folder scope: `in-review` or `refined` (reads all items in that folder)
- No input: reads everything in `backlog/backlog-items/in-review/` and `backlog/backlog-items/refined/`

**Resolving items:**
1. Read `backlog/counter.json`
2. For ID list: find and read each file
3. For folder scope: filter index by status, read all matching files

---

## Phase 1: Quality Evaluation

For each item, run the appropriate rubric from `evaluate-item.md` (inline — do not spawn a separate skill).

Output the scorecard for each item.

---

## Phase 2: Readiness Classification

After evaluating all items, produce three lists:

### ✅ Ready for Sprint ([N] items)
Items scoring ≥ 9.0 (Stories) or passing all gates (Bugs/Spikes/Tasks).
List each with ID, title, grade, and size.

### ⚠️ Fix Before Sprint ([N] items)
For each:
- **{ID} — {Title}** — Grade {X}/10
- Top 2 fixes: [specific and actionable]
- Effort: Quick (wording only) / Medium (add AC or BDD) / High (needs split or rewrite)
- Command: `/backlog:refine-item {ID}`

### ❌ Pull from Sprint ([N] items)
For each:
- **{ID} — {Title}** — Grade {X}/10
- Reason: [why it cannot go in the sprint as-is]
- Action: Return to `/backlog:generate-items` with [specific richer context needed] / Use `/backlog:split-item {ID}`

---

## Phase 3: Dev-Ready Checklist (✅ items only)

For each ready item, run the pre-handoff checklist from `dev-ready-handoff.md` (inline):

**Basics:** title is action statement / persona specific / value stated / size S or M
**AC:** declarative / happy path / error states / empty state / no shape prescription
**Technical context:** domain entities by business name / NFR thresholds / security
**Dependencies:** blockers identified / design assets linked / all resolved or dated

Flag any checklist failures with the specific item and item ID.

---

## Phase 4: Gap Detection (✅ items only)

For each dev-ready item, identify any information a developer would need that is NOT in the item — same as Step 2 in `dev-ready-handoff.md`.

List gaps per item. Ask the user to resolve before handoff.

---

## Phase 5: Save and Update Files

For each item that passed Phases 3 and 4:
1. Write `backlog/reports/{ID}-sprint-prep-{YYYY-MM-DD}.md` with the checklist + gap results
2. Update frontmatter: `status` → `ready`, `reports.handoff` → report path, `updated_at`
3. Move file to `backlog/backlog-items/ready/`
4. Update `counter.json`

For items that failed: update `updated_at` only. Do not move.

---

## Phase 6: Sprint Kickoff Note

Generate a ready-to-send note covering all ✅ items:

```
**[Epic Name] — Sprint [N] Kickoff**

What we're building: [1–2 sentences]
Why it matters: [1 sentence — business goal and success metric]

Items in this sprint ([N] total):
- ✅ {ID} — {Title} (S/M)
- ✅ {ID} — {Title} (S/M)

Dependency order: [sequence with brief reasoning]
Key constraints: [2–3 bullet points]
Contacts: Design ([name]), PM ([name])
[Link to epic in tracker]

Questions: drop them in [Slack channel] or tag me on the item.
```

---

## Sprint Health Summary

End with:
- Items ready vs. fix vs. pulled (counts and percentages)
- Most common quality issue across the batch
- One recommendation for the next sprint's preparation cycle

If more than 30% of items were pulled or require rework, tell the user: "Consider running `/backlog:audit-items` earlier — ideally during backlog grooming, not the day before sprint planning."

After passing items are in `ready/`, suggest: "Run `/backlog:jira-push {EPIC_ID}` to create the epic and all linked ready items in Jira at once."
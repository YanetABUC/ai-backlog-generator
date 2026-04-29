# Story Evaluation Template

Use this template to document the evaluation of a batch of user stories. Run this before every grooming session or sprint planning.

---

```markdown
# Story Evaluation Report

**Product / Epic:** [name]  
**Evaluator:** [PM/BA name]  
**Date:** [date]  
**Stories evaluated:** [number]  
**Sprint target:** [sprint or quarter]

---

## Summary

| Stories | Count |
|---|---|
| Dev-ready (grade >= 9.0) | |
| Needs refinement (7.0–8.9) | |
| Requires rework (grade < 7.0) | |
| **Total** | |

**Batch ready for sprint:** Yes / No / Partially (N of N stories ready)

---

## Individual Story Evaluations

### Story 1: [Title]

| Dimension | Score | Issue / Note |
|---|---|---|
| User Clarity | /3 | |
| Business Value | /3 | |
| AC Quality | /3 | |
| BDD Scenarios | /3 | |
| Story Size | /3 | |
| Edge Cases | /3 | |
| Dependency Clarity | /3 | |
| Conciseness / NFR / Out of Scope | /3 | |
| **Total** | **/24** | |
| **Grade** | **/10** | |

**Status:** ✅ Dev-ready (>= 9.0) / ⚠️ Needs work (7.0–8.9) / ❌ Requires rework (< 7.0)

**Required fixes:**
1. [specific fix — e.g., "Story starts with 'As a user' — rewrite to lead with 'In order to' and a specific business goal"]
2. [specific fix]

**Assigned to:** [who will make the fix]  
**Fix by:** [date]

---

### Story 2: [Title]

| Dimension | Score | Issue / Note |
|---|---|---|
| User Clarity | /3 | |
| Business Value | /3 | |
| AC Quality | /3 | |
| BDD Scenarios | /3 | |
| Story Size | /3 | |
| Edge Cases | /3 | |
| Dependency Clarity | /3 | |
| Conciseness / NFR / Out of Scope | /3 | |
| **Total** | **/24** | |
| **Grade** | **/10** | |

**Status:** ✅ Dev-ready (>= 9.0) / ⚠️ Needs work (7.0–8.9) / ❌ Requires rework (< 7.0)

**Required fixes:**
1.
2.

**Assigned to:**  
**Fix by:**

---

*(Repeat for each story in the batch)*

---

## Common Issues Found

*List patterns that appeared across multiple stories — these indicate process gaps.*

1. [common issue — e.g., "8 of 10 stories missing error state criteria"]
2. [common issue]
3. [common issue]

---

## Process Recommendations

*Based on this evaluation, what should change in how stories are written?*

- [ ] [recommendation — e.g., "Add AC generation step to story creation workflow"]
- [ ] [recommendation]

---

## Business Accuracy Review

*Human review — cannot be automated.*

Stories confirmed accurate by PM/BA:
- [ ] Story 1 — business logic confirmed against [source: user research / stakeholder decision / product brief]
- [ ] Story 2
- [ ] ...

Stories needing business correction:
- [ ] Story N — issue: [describe what's inaccurate and correct version]

---

## Technical Feasibility Review

*Engineering review — conducted by senior engineer or tech lead.*

Reviewed by: [name]  
Date: [date]

| Story | Feasible | Size Accurate | Flag |
|---|---|---|---|
| Story 1 | Yes / No | Yes / No | [issue if any] |
| Story 2 | | | |

Engineering notes:
- [note]

---

## Final Decision

**Stories approved for sprint:**
- Story 1, Story 2, Story 4, Story 5

**Stories returned for rework:**
- Story 3 — [reason]
- Story 6 — [reason]

**Sprint planning cleared:** Yes / No  
**Next review date:** [date]
```

# Workflow 07: Semantic Review

**Time:** 20–40 minutes per story (includes human verification of flagged questions)
**Input:** A user story that has passed structural evaluation (grade ≥ 7.0)
**Output:** A risk register of claims requiring human verification, saved to `backlog/reports/`

---

## Overview

Structural evaluation checks whether a story is well-formed. Semantic review checks whether it is true.

A story can pass all 14 rapid-audit gates, score 9.0 on the 8-dimension rubric, and still send engineering in the wrong direction — because an AC item was an untested inference, because the full set of ACs doesn't actually prove the user goal, or because a behavioral rule in this story contradicts a rule in a sibling story from the same epic.

These are the failures that cause sprint rework, missed acceptance, and the "but I thought we agreed" conversations in review. Structural evaluation can't catch them. Only reasoning about meaning can.

---

## Where This Fits in the Pipeline

```
Discovery → Assumption Validation → Generation → Structural Evaluation → Semantic Review → Dev-Ready Handoff → Jira Sync
```

Semantic review sits between structural evaluation and handoff. Run it on stories that scored ≥ 7.0 — stories below that threshold have structural problems that should be fixed first.

---

## When to Use This

- After `/backlog:evaluate-item` returns a grade ≥ 7.0
- Before `/backlog:dev-ready-handoff` on any story with significant behavioral detail
- When a story was written far from the original discovery session (context drift risk is higher)
- When the discovery record was thin or assumptions were validated informally
- When a story was manually written or imported from Jira without going through the generation pipeline
- During backlog grooming when a story "looks complete" but generates debate in review

---

## Step 1: Confirm Prerequisites

Before running semantic review, verify:

- [ ] The story has passed structural evaluation (grade ≥ 7.0 on the 8-dimension rubric)
- [ ] The parent epic exists and is accessible
- [ ] The discovery record for this epic exists in `backlog/discovery/` (if not, note it — some checks will be limited)

You do not need to gather sibling stories manually — the skill reads them from `counter.json`.

---

## Step 2: Run the Skill

```
/backlog:semantic-review US-{ID}
```

Or through the agent:
> "Run a semantic review on US-{ID}"

The skill will:
1. Load the item, parent epic, discovery record, and all sibling stories in the same epic
2. Run five checks: claim traceability, goal-AC alignment, BDD-AC alignment, cross-story consistency, inference audit
3. Produce a risk register classified as High / Medium / Low
4. Save the report to `backlog/reports/{ID}-semantic-{YYYY-MM-DD}.md`
5. Return a verdict: proceed to handoff or resolve High risks first

---

## Step 3: Read the Risk Register

The output is a risk register, not a score. Read each flagged item carefully:

**High Risk** items will likely cause sprint rework if not resolved. Do not proceed to handoff with open High risks.

**Medium Risk** items may cause misalignment during development. Review them with the team — most can be resolved with a quick stakeholder confirmation or a clarifying AC update.

**Low Risk** items are observations worth tracking but unlikely to block delivery.

---

## Step 4: Resolve Flagged Questions

Each risk item includes a **Verify** question — the specific thing a PM, BA, or domain expert must confirm. These questions cannot be resolved by re-running the skill or refining the story text. They require a human who knows the domain.

For each High and Medium risk:

1. Answer the verify question — through a stakeholder conversation, a review of discovery notes, or a check against the product brief
2. If the answer changes the story, update the item file: correct the AC item, add a missing AC, adjust the narrative, or remove the untraceable claim
3. If the answer confirms the story is correct, add a brief note in the item's narrative or discovery record so the traceability is on record

---

## Step 5: Handle Common Findings

### Untraceable AC item
The AC was inferred during writing and has no source in discovery or the epic.
- If the behavior is correct: add a validated assumption to the epic's constraints section, then it becomes traceable
- If the behavior is uncertain: replace it with a spike or tag it as an open question before sprint

### Goal-AC gap (sufficiency failure)
The "so that" goal would not be achieved even if all ACs pass.
- Add the missing AC item that covers the gap
- If the gap is large, consider whether the story is actually complete or should be split

### Orphaned BDD scenario
A BDD scenario tests behavior not stated in any AC.
- Either add the corresponding AC item (if the behavior is required)
- Or remove the scenario (if it tests out-of-scope behavior)

### Cross-story contradiction
Two stories in the same epic state conflicting rules.
- Identify which story is authoritative (usually the one closer to the discovery record)
- Update the other story to align
- If the contradiction reflects a genuine unresolved product decision, create a spike to resolve it before either story enters sprint

### Unvalidated inference
A claim is stated as fact but was never validated.
- If it came from implicit domain knowledge: document the source in the discovery record
- If it came from assumption: mark it in the epic as `[To validate]` and block the story until resolved
- If it is clearly wrong: correct the story

---

## Step 6: Re-run If High Risks Were Found

After updating the item file to resolve High risks:

```
/backlog:semantic-review US-{ID}
```

Confirm the High risks no longer appear. Then proceed to handoff.

---

## Step 7: Proceed to Handoff

When semantic review returns no High risks:

```
/backlog:dev-ready-handoff US-{ID}
```

---

## What Semantic Review Does Not Replace

| This step | Is still required because |
|---|---|
| Structural evaluation (`/backlog:evaluate-item`) | Semantic review assumes the story is well-formed. Run it first. |
| Engineering feasibility review | Semantic review does not catch technical constraints or hidden complexity. A senior engineer still needs to confirm feasibility. |
| Stakeholder sign-off | The risk register surfaces questions — humans answer them. The skill cannot resolve semantic truth. |

---

## Quick Reference

| Finding type | Where it shows | Human action |
|---|---|---|
| Untraceable AC | High or Medium | Confirm with stakeholder or add validated assumption to epic |
| Goal not achieved if all ACs pass | High | Add missing AC item |
| BDD tests behavior outside AC | Medium | Add AC or remove scenario |
| AC has no BDD scenario | Medium | Add the missing scenario |
| Sibling story contradiction | High | Align stories; create spike if unresolved |
| Unvalidated inference | High or Medium | Validate with user research or domain expert |

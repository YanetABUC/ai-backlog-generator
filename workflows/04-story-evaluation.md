# Workflow 04: Story Evaluation

**Time:** 30–60 minutes per batch  
**Input:** Draft user stories (from any source — AI-generated, manually written, or imported)  
**Output:** Scored stories with specific improvement recommendations

---

## Overview

Story evaluation is a quality gate, not a nice-to-have. It's the step that catches vague requirements, missing edge cases, and untestable acceptance criteria before they reach engineering.

This workflow applies both AI and human judgment. AI evaluates structure and completeness. Humans evaluate business accuracy and technical feasibility.

---

## When to Use This

- After generating stories with AI (always)
- Before sprint planning
- During backlog grooming as a quality audit
- When inheriting a backlog from another team
- When stories are getting pushed back during sprint

---

## Step 1: Batch Your Stories

Group stories by epic or feature area before evaluating. Evaluating in context produces better results than evaluating stories in isolation.

Ideal batch size: 5–10 stories.

---

## Step 2: Rapid Evaluation Filter

Run the pass/fail filter first. Use the Rapid Evaluation variant in [evaluate-story-quality.md](../prompts/evaluate-story-quality.md) to identify stories with obvious structural failures before spending tokens on the full rubric.

Stories that fail any gate go directly to refinement or back to story generation. Do not run the full rubric on them.

Stories that pass all gates move to Step 3.

---

## Step 3: Run Full 8-Dimension Evaluation

For stories that passed the Rapid Evaluation filter, run the full scored evaluation using the Core Evaluation Prompt in [evaluate-story-quality.md](../prompts/evaluate-story-quality.md).

Scoring: grade = round(Total / 24 × 10, 1)

- Grade ≥ 9.0: Dev-ready — proceed to human review
- Grade 7.0–8.9: Needs refinement — apply targeted fixes using [refine-stories.md](../prompts/refine-stories.md)
- Grade below 7.0: Requires rework — return to story generation with better context

---

## Step 4: Review Scores and Prioritize Fixes

After AI evaluation:

1. Sort stories by grade (lowest first)
2. Stories below 7.0 — return to story generation; do not attempt to refine a weak draft
3. Stories graded 7.0–8.9 — apply targeted fixes using the refinement prompt
4. Stories graded 9.0+ — proceed to human review

**Refinement cap: two passes per story.** If a story has not reached grade ≥ 7.0 after two refinement cycles, stop iterating and return it to story generation with richer input context. See [refine-stories.md](../prompts/refine-stories.md) for the iteration limit rule.

See also: [refine-stories.md](../prompts/refine-stories.md)

---

## Step 5: Human Business Accuracy Review

AI evaluates structure. Humans evaluate accuracy. For each story, a PM or BA confirms:

- [ ] The user persona reflects a real user type (not AI-invented)
- [ ] The "so that" clause reflects actual user motivation (from research, not assumption)
- [ ] The acceptance criteria match how the product actually behaves
- [ ] The business value is connected to a real metric or goal
- [ ] Nothing in the story contradicts known stakeholder decisions
- [ ] The scope matches what was agreed, not what AI inferred

Mark each story: **Accurate** / **Needs correction** / **Factually wrong**

Correct any inaccuracies before moving to engineering review.

---

## Step 6: Technical Feasibility Review

For stories that passed AI and human review, have a senior engineer perform a quick scan:

**Questions for engineering:**
1. Is this story technically feasible as written?
2. Are there hidden complexities not reflected in the story?
3. Are there dependencies (data, APIs, services) that the story doesn't mention?
4. Is the size estimate (S/M/L) reasonable given the implementation?
5. Does anything in the acceptance criteria conflict with the system's current behavior?

Capture feedback in the story's technical notes field. Adjust acceptance criteria or add a dependency if needed.

---

## Step 7: Edge Case Completeness Check

Run a dedicated edge case check on stories that passed all prior reviews:

**Prompt:**
```
For the following user story, identify all edge cases that must be handled but are not currently covered in the acceptance criteria.

Consider:
- Empty state (no data available)
- Maximum data state (large datasets, long strings, many items)
- Permission edge cases (user has partial permissions, permissions change mid-flow)
- Network/API failures (what happens when a downstream call fails?)
- Concurrent actions (what if two users do this at the same time?)
- Invalid input (malformed data, special characters, injections)
- State conflicts (what if the record is in an unexpected state when this action is taken?)
- Time-based edge cases (timezone differences, expiry during a session)

User story:
[paste story]

For each edge case:
1. Describe the scenario
2. State the expected behavior
3. Write it as a Given/When/Then acceptance criterion
```

Add the relevant edge cases to the story's acceptance criteria. Not every edge case needs its own story — most become additional criteria.

---

## Step 8: Final Quality Gate

Before marking a story as "Ready":

```
[ ] Grade ≥ 9.0/10 on 8-dimension rubric
[ ] Business accuracy confirmed by PM/BA
[ ] Technical feasibility confirmed by engineering
[ ] All edge cases covered in acceptance criteria
[ ] Dependencies are listed and confirmed available
[ ] No open questions remain (or they're tracked separately)
[ ] Definition of Done is documented on the story
[ ] Story is appropriately sized for one sprint
```

---

## Evaluation Summary Template

Use this to document evaluation results for a batch:

```markdown
## Story Evaluation Summary

Batch: [epic or feature name]
Date: [date]
Evaluator: [name]

| Story | Score | Status | Key Issues |
|---|---|---|---|
| [story title] | /10 | Dev-ready / Needs refinement / Rework | |

Issues found:
- [issue]

Actions taken:
- [action]

Ready for sprint: [Y/N]
```

---

## Common Patterns That Lead to Low Scores

| Pattern | Dimension | Fix |
|---|---|---|
| "As a user, I want..." | User Clarity | Define the persona: role, context, goal |
| "So that it works correctly" | Business Value | State the outcome: speed, revenue, satisfaction |
| "The system should..." in AC | AC Quality | Rewrite as Given/When/Then |
| Story spans multiple subsystems | Story Size | Split at the layer boundary |
| Only happy path in AC | Edge Cases | Add empty, error, and permission scenarios |
| "Depends on design" | Dependency Clarity | Get design link or create a design story first |

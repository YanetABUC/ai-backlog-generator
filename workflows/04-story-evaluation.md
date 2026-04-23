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

## Step 2: Run AI Structural Evaluation

**Prompt:**
```
You are a senior business analyst evaluating user stories before they enter a development sprint.

Evaluate each story against the following quality rubric. Score each dimension 1–3:

1. **User Clarity** (1=generic user, 2=typed user, 3=specific named persona with context)
2. **Business Value** (1=no value stated, 2=value implied, 3=measurable outcome stated)
3. **Acceptance Criteria Quality** (1=vague/subjective, 2=mostly testable, 3=all Given/When/Then, all pass/fail)
4. **Story Size** (1=too large for one sprint, 2=borderline, 3=completable in 1–3 days)
5. **Edge Cases** (1=happy path only, 2=some edge cases, 3=happy + error + edge cases covered)
6. **Dependency Clarity** (1=dependencies unknown, 2=some identified, 3=all dependencies explicit)

For each story:
- Assign a score per dimension
- Calculate total (max 18)
- Flag each dimension scoring below 2
- For each flagged dimension, provide a specific fix (not generic advice)

Scoring interpretation:
- 16–18: Dev-ready
- 12–15: Minor refinement needed
- Below 12: Significant rework required

Stories to evaluate:
[paste stories]

Context (product/domain):
[brief description of product and users]
```

---

## Step 3: Review Scores and Prioritize Fixes

After AI evaluation:

1. Sort stories by score (lowest first)
2. Stories scoring below 12 — rewrite before proceeding
3. Stories scoring 12–15 — apply targeted fixes using the AI's recommendations
4. Stories scoring 16–18 — proceed to human review

**Don't fix every story manually.** For stories below 12, use the refinement prompt:

```
Rewrite the following user story to improve its quality. 

The story scored low on: [list dimensions with low scores]
Specific issues identified: [paste AI feedback]

Rewrite requirements:
- Keep the core intent
- Make the user persona specific
- Make the business value measurable
- Convert acceptance criteria to Given/When/Then format
- Add at least 2 edge cases
- Identify any dependencies

Original story:
[paste story]

Domain context:
[paste product context]
```

See also: [refine-stories.md](../prompts/refine-stories.md)

---

## Step 4: Human Business Accuracy Review

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

## Step 5: Technical Feasibility Review

For stories that passed AI and human review, have a senior engineer perform a quick scan:

**Questions for engineering:**
1. Is this story technically feasible as written?
2. Are there hidden complexities not reflected in the story?
3. Are there dependencies (data, APIs, services) that the story doesn't mention?
4. Is the size estimate (S/M/L) reasonable given the implementation?
5. Does anything in the acceptance criteria conflict with the system's current behavior?

Capture feedback in the story's technical notes field. Adjust acceptance criteria or add a dependency if needed.

---

## Step 6: Edge Case Completeness Check

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

## Step 7: Final Quality Gate

Before marking a story as "Ready":

```
[ ] Total evaluation score 16+/18
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
| [story title] | /18 | Ready / Needs work | |

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

# Prompt: Evaluate Story Quality

Use this prompt to run a structured quality evaluation on user stories before they enter a sprint.

---

## When to Use

- After AI-generated stories are produced
- During backlog grooming as a quality audit
- Before sprint planning
- When inheriting stories from another team or tool
- As part of a Definition of Ready gate

---

## Core Evaluation Prompt

```
You are a senior business analyst and QA lead performing a pre-sprint story evaluation.

Evaluate each of the following user stories against the quality rubric below. Your goal is to identify stories that are NOT dev-ready and provide specific, actionable fixes — not generic advice.

## Quality Rubric

Score each dimension 1–3:

### 1. User Clarity (1–3)
- 1 = Generic "user" or no persona
- 2 = Role identified but no context
- 3 = Specific persona with role + context + implied goal

### 2. Business Value (1–3)
- 1 = No "so that" clause, or it's circular ("so that it works")
- 2 = Value stated but not measurable ("so that I can be more efficient")
- 3 = Measurable outcome tied to a real user goal or business metric

### 3. Acceptance Criteria Quality (1–3)
- 1 = Vague or missing ("system should work as expected")
- 2 = Partially testable (some pass/fail, some subjective)
- 3 = All criteria in Given/When/Then format, all pass/fail

### 4. Story Size (1–3)
- 1 = Too large — spans multiple subsystems or > 5 days
- 2 = Borderline — could fit in sprint but risky
- 3 = Clearly sprint-ready — 1–3 days of focused work

### 5. Edge Case Coverage (1–3)
- 1 = Happy path only
- 2 = Happy path + some edge cases
- 3 = Happy path + error state + at least 2 edge cases

### 6. Dependency Clarity (1–3)
- 1 = Dependencies not mentioned or unknown
- 2 = Some dependencies identified, some unclear
- 3 = All dependencies explicitly listed (APIs, design assets, other stories)

## Output Format for Each Story

**[Story Title]**
| Dimension | Score | Issue |
|---|---|---|
| User Clarity | /3 | [specific issue or "meets bar"] |
| Business Value | /3 | [specific issue or "meets bar"] |
| AC Quality | /3 | [specific issue or "meets bar"] |
| Story Size | /3 | [specific issue or "meets bar"] |
| Edge Cases | /3 | [specific issue or "meets bar"] |
| Dependencies | /3 | [specific issue or "meets bar"] |
| **Total** | **/18** | |

**Status:** Dev-Ready (16–18) / Needs Refinement (12–15) / Requires Rework (below 12)

**Top 2 fixes needed:**
1. [specific fix with example]
2. [specific fix with example]

---

## Stories to Evaluate

[paste stories here]

## Context

**Product:** [brief description]
**Domain:** [relevant area]
**User types in this product:** [list user roles]
```

---

## Variant: Rapid Evaluation (Pass/Fail Only)

For teams that need a quick filter before detailed review:

```
Evaluate the following stories with a simple pass/fail on each quality gate.

A story FAILS if any of the following are true:
- User is generic ("user", "admin", "customer" with no context)
- "So that" clause is missing, circular, or doesn't state a user outcome
- Acceptance criteria are not in Given/When/Then format
- No error state is defined
- The story requires more than one PR across different services
- At least one dependency is undefined

For each story:
- List which gates it fails (or "PASS — all gates")
- If it fails any gate, provide the specific fix for each failure

Stories:
[paste]
```

---

## Variant: Comparative Evaluation (Before vs. After)

```
Compare the following two versions of a user story and evaluate which is higher quality.

For each version, score it on the 6-dimension rubric (1–3 each).

Identify specifically what improved from Version A to Version B, and what (if anything) regressed.

Version A (original):
[paste]

Version B (refined):
[paste]
```

---

## Variant: Batch Scorecard

For evaluating 10+ stories at once:

```
Evaluate the following stories and output a summary scorecard.

For each story, provide:
- Story title
- Score per dimension (abbreviated: UC, BV, AC, SS, EC, DC)
- Total score /18
- Status: ✅ Dev-ready / ⚠️ Needs work / ❌ Rework

After the scorecard, list the top 3 most common issues across all stories.

Stories:
[paste]
```

---

## Scoring Interpretation

| Total Score | Status | Action |
|---|---|---|
| 16–18 | Dev-ready | Proceed to engineering handoff |
| 14–15 | Minor issues | Apply targeted fixes, no full rewrite |
| 12–13 | Moderate issues | Rewrite weak sections using refinement prompts |
| 10–11 | Significant issues | Substantial rewrite needed |
| Below 10 | Poor quality | Return to story generation with better context |

---

## Example Evaluation Output

**Story: "As a user, I want to see my reports so that I can make decisions."**

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | 1/3 | "User" has no role or context — who generates reports and for what purpose? |
| Business Value | 1/3 | "Make decisions" is circular — what decision? What outcome? |
| AC Quality | 1/3 | No acceptance criteria provided |
| Story Size | 2/3 | Reports is a broad feature — unclear scope |
| Edge Cases | 1/3 | No criteria = no edge cases covered |
| Dependencies | 1/3 | Reporting data source not mentioned |
| **Total** | **7/18** | |

**Status:** ❌ Requires Rework

**Top 2 fixes:**
1. Replace "user" with a specific persona: "As an operations manager reviewing weekly fulfillment data..." and tie the outcome to a measurable action: "...so that I can identify fulfillment delays before the daily shipping cutoff."
2. Add at least 5 Given/When/Then acceptance criteria covering: what reports are displayed, how they're filtered, the empty state, the error state, and at least one role-based behavior.

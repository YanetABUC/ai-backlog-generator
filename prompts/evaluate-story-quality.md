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
- 1 = Story does not start with "In order to", or the business goal is circular / a feature description
- 2 = "In order to" present but vague; persona identified but no role context
- 3 = Leads with a specific business goal; persona is role + context, not just "user"

### 2. Business Value (1–3)
- 1 = No "so that" clause, or it's circular ("so that it works")
- 2 = Value stated but not measurable ("so that I can be more efficient")
- 3 = Measurable outcome tied to a real user goal or business metric

### 3. Acceptance Criteria Quality (1–3)
- 1 = Vague or missing ("system should work as expected") or written as Given/When/Then (those are BDD Scenarios, not AC)
- 2 = Partially testable — some pass/fail conditions, some subjective or compound
- 3 = All criteria are declarative pass/fail statements; each is independent and atomic; no implementation details

### 4. BDD Scenarios (1–3)
- 1 = Missing or only Happy Path present
- 2 = At least two scenario types present, but error text is unquoted or a scenario has multiple When actions
- 3 = All four types present (Happy Path, Validation, Edge Case, Failure Handling); each has a single When; error messages are quoted; Failure Handling confirms data is preserved

### 5. Story Size (1–3)
- 1 = Too large — spans multiple subsystems or > 5 days
- 2 = Borderline — could fit in sprint but risky
- 3 = Clearly sprint-ready — 1–3 days of focused work

### 6. Edge Case Coverage (1–3)
- 1 = Happy path only
- 2 = Happy path + some edge cases
- 3 = Happy path + error state + at least 2 edge cases with explicit threshold behavior

### 7. Dependency Clarity (1–3)
- 1 = Dependencies not mentioned or unknown
- 2 = Some dependencies identified, some unclear
- 3 = All dependencies explicitly listed (APIs, design assets, other stories)

### 8. Conciseness, NFRs, and Out of Scope (1–3)
- 1 = Separate Business Objective / Problem Context / Desired Outcome sections present; or a Functional Requirements section exists; or shape prescription found (return type enumerations, outcome variants, result structures); or story exceeds 200 lines
- 2 = Context is one paragraph but slightly verbose; NFRs present but some are boilerplate; Out of Scope present but thin; no shape prescription
- 3 = Story is under 150 lines; context is one concise paragraph; no FR section; no shape prescription in any section; NFRs are project-specific with measurable thresholds; Out of Scope explicitly lists exclusions

## Output Format for Each Story

Grade formula: `round(Total / 24 × 10, 1)`

**[Story Title]**
| Dimension | Score | Issue |
|---|---|---|
| User Clarity | /3 | [specific issue or "meets bar"] |
| Business Value | /3 | [specific issue or "meets bar"] |
| AC Quality | /3 | [specific issue or "meets bar"] |
| BDD Scenarios | /3 | [specific issue or "meets bar"] |
| Story Size | /3 | [specific issue or "meets bar"] |
| Edge Cases | /3 | [specific issue or "meets bar"] |
| Dependencies | /3 | [specific issue or "meets bar"] |
| Conciseness / NFR / Out of Scope | /3 | [specific issue or "meets bar"] |
| **Total** | **/24** | |
| **Grade** | **/10** | |

**Status:** Dev-Ready (>= 9.0) / Needs Refinement (7.0–8.9) / Requires Rework (below 7.0)

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
- Story does not start with "In order to", or the business goal is a feature description rather than an outcome
- User is generic ("user", "admin", "customer" with no context)
- "So that" clause is missing, circular, or doesn't state a user outcome
- Acceptance criteria use Given/When/Then format (those are BDD Scenarios, not AC)
- Acceptance criteria are vague or not independently pass/fail
- Not all four BDD scenario types are present: Happy Path, Validation, Edge Case, Failure Handling
- No error state is defined
- The story requires more than one PR across different services
- At least one dependency is undefined
- Story contains a Functional Requirements (FR1, FR2...) section — observable behaviors belong in AC
- Separate Business Objective, Problem Context, or Desired Outcome sections exist — should be one Context paragraph
- Shape prescription found: return type enumerations, outcome variants ("return one of X / Y / Z"), result object structures, or function signatures in any section
- Non-Functional Requirements are missing or lack specific thresholds
- Out of Scope section is absent

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

For each version, score it on the 8-dimension rubric (1–3 each).

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
- Score per dimension (abbreviated: UC, BV, AC, BDD, SS, EC, DC, CS)
- Total /24 and grade /10 (= round(total / 24 × 10, 1))
- Status: ✅ Dev-ready (>= 9.0) / ⚠️ Needs work (7.0–8.9) / ❌ Rework (< 7.0)

After the scorecard, list the top 3 most common issues across all stories.

Stories:
[paste]
```

---

## Scoring Interpretation

Grade = round(Total / 24 × 10, 1)

| Grade | Status | Action |
|---|---|---|
| 9.0–10.0 | Dev-ready | Proceed to engineering handoff |
| 7.0–8.9 | Needs refinement | Apply targeted fixes using refine-stories.md |
| Below 7.0 | Requires rework | Rewrite required — return to story generation with better context |

---

## Example Evaluation Output

**Story: "As a user, I want to see my reports so that I can make decisions."**

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | 1/3 | Story starts with "As a user" — missing "In order to" business goal; "user" has no role or context |
| Business Value | 1/3 | "Make decisions" is circular — what decision? What measurable outcome? |
| AC Quality | 1/3 | No acceptance criteria provided |
| BDD Scenarios | 1/3 | No scenarios of any type provided |
| Story Size | 2/3 | Reports is a broad feature — scope unclear |
| Edge Cases | 1/3 | No criteria = no edge cases covered |
| Dependencies | 1/3 | Reporting data source not mentioned |
| Conciseness / NFR / Out of Scope | 1/3 | No NFRs, no Out of Scope, no Context paragraph |
| **Total** | **9/24** | |
| **Grade** | **3.8/10** | |

**Status:** ❌ Requires Rework (grade 3.8 — below 7.0)

**Top 2 fixes:**
1. Rewrite to lead with a business goal and a specific persona: "In order to identify fulfillment delays before the daily shipping cutoff, As an operations manager reviewing weekly fulfillment data, I want to view a filtered report of unfulfilled orders, So that I can escalate at-risk orders before the carrier pickup window closes."
2. Add at least 5 declarative AC items (not Given/When/Then) covering: what is displayed, how it is filtered, the empty state, the error state, and at least one access-control behavior — then add the four required BDD scenario types to prove each AC item is testable.

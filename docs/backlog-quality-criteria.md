# Backlog Quality Criteria

A story that isn't dev-ready isn't done — it's deferred work disguised as progress. This document defines the quality bar every item in your backlog should meet before entering a sprint.

---

## The Seven Quality Dimensions

### 1. Clarity of User and Context

**What to check:**
- Is the user persona specific, not generic? ("Admin user managing multi-location inventory" vs. "user")
- Is the context (when, where, why) explicit?
- Would a new team member understand who this is for?

**Red flags:**
- "As a user, I want..." with no persona definition
- Ambiguous "they" or "the system"
- Missing trigger or context for the action

**Good example:**
> As a warehouse manager overseeing 3+ locations, I want to filter low-stock alerts by location so that I can prioritize replenishment without cross-location noise.

---

### 2. Business Value Is Explicit

**What to check:**
- Is the "so that" clause tied to a measurable outcome?
- Could you explain why this story matters to a non-technical stakeholder in one sentence?
- Does the value justify the effort estimate?

**Red flags:**
- "So that the system works correctly" (not a business outcome)
- No connection to an OKR, KPI, or user pain point
- Value is assumed, not stated

**Good example:**
> So that we reduce stockout incidents by surfacing location-specific data before managers escalate via support tickets.

---

### 3. Acceptance Criteria Are Verifiable

**What acceptance criteria are:** A checklist of declarative conditions — each one an independent, present-tense statement of observable behavior that must be true for the story to be "done." They are not test scripts and do not use Given/When/Then.

**What to check:**
- Is each criterion a declarative statement (not a narrative or a step)?
- Can each criterion be answered with a clear yes/no — did it happen or not?
- Is every criterion independent — evaluable without reading the others?
- Are specific values, thresholds, and states named (not implied)?

**Red flags:**
- "Works as expected" — not verifiable, no observable bar defined
- "The system should perform well" — not measurable, no threshold
- Criteria written as Given/When/Then — those are BDD Scenarios, not AC
- Two conditions combined in one bullet — each criterion must be atomic
- Implementation detail in the criterion — "the API returns 200" is not observable behavior

**Good examples:**
> - A manager can approve a pending suggestion after reviewing a confirmation summary showing vendor name, quantity, and estimated value
> - Approved suggestions valued under $5,000 are immediately available for download as a PO document
> - When approval fails due to a system error, the suggestion status remains unchanged

**Bad examples:**
> - "Given the user clicks Approve, when confirmed, then..." ← that's a BDD Scenario
> - "The approval works correctly" ← not verifiable
> - "The system calls the /approve endpoint" ← implementation detail

---

### 4. BDD Scenarios Are Testable

**What BDD scenarios are:** Concrete, executable narratives in Given/When/Then format that prove the Acceptance Criteria are met in specific situations. Every story requires exactly four scenario types.

**What to check:**
- Are all four scenario types present: Happy Path, Validation, Edge Case, Failure Handling?
- Is each scenario driven by a single user action (one When per scenario)?
- Do Validation and Failure Handling scenarios specify the exact error message text?
- Do Failure Handling scenarios confirm that data is preserved and the user can retry?
- Do Edge Case scenarios state threshold behavior explicitly (inclusive or exclusive)?
- Does every AC item map to at least one scenario?

**Red flags:**
- Only Happy Path scenario — validation, edge cases, and failures are missing
- "Then the error is displayed" — what error? What text? Must be quoted
- "When the user does A and then B" — two actions in one scenario, must be split
- Scenario name is "Success case" or "Error case" — names must describe the situation
- Boundary condition doesn't say whether the threshold is inclusive or exclusive
- No confirmation that data is preserved in a Failure Handling scenario

**Required format:**

```gherkin
Scenario: [Readable description of the specific situation]
  Given [precondition — state of the world before the user acts]
  And [additional precondition if needed]
  When [the single action the user or system performs]
  Then [the observable result — specific, not "the system handles it"]
  And [secondary result or side effect]
```

**Four required types:**

| Type | What it covers |
|---|---|
| Happy Path | Primary action succeeds under normal conditions |
| Validation | Invalid input or business rule violation is correctly rejected |
| Edge Case | Boundary condition or unusual but valid scenario is handled correctly |
| Failure Handling | System or integration failure is recovered from gracefully |

**Prompt:** See [generate-bdd-scenarios.md](../prompts/generate-bdd-scenarios.md)

---

### 5. Right Size (Fits in a Sprint)

**What to check:**
- Can this be completed in 1–3 days of focused work?
- If the story spans multiple subsystems, should it be split?
- Is there a vertical slice of value (not just a layer of the stack)?

**Size signals:**
| Signal | Action |
|---|---|
| "And also..." appears in the title | Split the story |
| Estimate > 8 points | Split the story |
| Requires multiple PRs across services | Split or create a spike first |
| Team can't estimate without a discovery session | Create a spike story |

**Splitting prompts:** See [split-stories.md](../prompts/split-stories.md)

---

### 6. Edge Cases Are Identified

**What to check:**
- What happens when the data is empty?
- What happens when the user lacks permissions?
- What happens when an external service fails?
- What happens at scale (100 items vs. 100,000)?
- What happens on mobile vs. desktop (if applicable)?

Edge cases are often where bugs live. Surfacing them in the story — not during PR review — saves the most time.

**Edge case generation prompt:** See [identify-edge-cases.md](../prompts/identify-edge-cases.md)

---

### 7. No Ambiguous Dependencies

**What to check:**
- Are external dependencies (APIs, services, teams) identified?
- Is any required data available, or does it need to be created first?
- Are there stories that must ship before this one?
- Are design assets ready, or is that a blocker?

**Dependency field in story template:**
```
Dependencies:
- [ ] Design: Figma frame #123 approved
- [ ] Backend: /api/inventory/locations endpoint available
- [ ] Data: location_id field added to inventory schema
```

---

## Quick Quality Checklist

Use this before moving a story to "Ready for Sprint":

```
[ ] User persona is specific — role + context, not "user"
[ ] Business value is stated with a measurable "so that" clause
[ ] Acceptance Criteria are a checklist of declarative, pass/fail conditions
[ ] Every AC item is independent and atomic (no two conditions in one bullet)
[ ] No AC item uses Given/When/Then (those are BDD Scenarios)
[ ] All four BDD Scenario types are present: Happy Path, Validation, Edge Case, Failure Handling
[ ] Validation and Failure Handling scenarios specify the exact error message text
[ ] Failure Handling scenarios confirm data is preserved and the user can retry
[ ] Edge Case scenarios state threshold inclusivity explicitly
[ ] Every AC item maps to at least one BDD Scenario
[ ] Story can be completed in 1–3 days of focused work
[ ] At least 2 edge cases are covered across AC and scenarios
[ ] No undefined external dependencies
[ ] Definition of Done is explicit
[ ] An engineer unfamiliar with context could start work without a meeting
```

---

## Scoring Guide (for AI Evaluation)

When using AI to evaluate story quality, score each dimension 1–3:

| Score | Meaning |
|---|---|
| 3 | Meets the bar — no action needed |
| 2 | Partially meets — needs a specific fix |
| 1 | Does not meet — rewrite required |

**Total score interpretation:**
- 19–21: Dev-ready
- 15–18: Needs targeted refinement
- Below 15: Needs significant rework

See the [evaluation template](../templates/evaluation-template.md) and [evaluate-story-quality prompt](../prompts/evaluate-story-quality.md).
# Backlog Quality Criteria

A story that isn't dev-ready isn't done — it's deferred work disguised as progress. This document defines the quality bar every item in your backlog should meet before entering a sprint.

---

## The Eight Quality Dimensions

### 1. Clarity of User and Context

**What to check:**
- Does the story start with "In order to" — a specific business goal or outcome, not a feature description?
- Is the user persona specific, not generic? ("warehouse manager responsible for daily inventory replenishment at a single location" vs. "user")
- Is the context (when, where, why) explicit in the persona and "I want to" clause?
- Would a new team member understand who this is for and what they are trying to accomplish?

**Red flags:**
- Story starts with "As a user" — missing the "In order to" business goal
- "In order to" clause is circular ("In order to use the feature") — not a business outcome
- Persona is vague: "user", "admin", or "the system" with no role or context
- Ambiguous "they" or "the system" with no qualifier
- Missing trigger or context for the action

**Good example:**
> **In order to** maintain stock levels across all SKUs before vendor order cutoffs,
> **As a** warehouse manager responsible for daily inventory replenishment at a single location,
> **I want to** view a prioritized list of all pending purchase order suggestions for my location,
> **So that** I can review and act on every replenishment need before the daily vendor cutoff — without exporting or searching manually.

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

### 8. Conciseness, NFRs, and Out of Scope

**Why this dimension exists:** AI coding assistants treat every sentence in a story as a spec. Redundant sections dilute the signal and waste context. Shape prescription — enumerating return types, result structures, or outcome variants — causes the AI to build exactly that shape, even when a simpler design would work. This dimension checks that the story is lean, does not prescribe implementation, and is explicit about its boundaries.

**What to check (Conciseness and structure):**
- Is the story under 150 lines?
- Is context expressed in a single paragraph — not split into Business Objective, Problem Context, and Desired Outcome sections?
- Is there no separate Functional Requirements section? Observable behaviors belong in AC.
- Does any section contain shape prescription: return type enumerations, result object definitions, or function signatures?

**What to check (Non-Functional Requirements):**
- Are NFRs specific to this story — not boilerplate that applies to every story?
- Does each performance NFR include a specific number (response time, concurrency, volume)?
- Do security NFRs name specific roles and state what is enforced server-side?
- Do reliability NFRs describe the specific degraded-mode behavior for this feature?

**What to check (Out of Scope):**
- Is there an explicit list of what is *not* included in this story?
- Does it name adjacent features or Phase 2 items that stakeholders might assume are in scope?

**Red flags (Conciseness and structure):**
- Separate Business Objective, Problem Context, and Desired Outcome sections — should be one Context paragraph
- A Functional Requirements (FR1, FR2...) section — it duplicates AC and risks shape prescription
- "Return one of: Pre-answer / Block / Resolved" — shape prescription; engineers choose the structure
- "The system must call the /approve endpoint" — implementation detail in any section
- Story exceeds 200 lines without justification

**Red flags (Non-Functional Requirements):**
- "The feature must be fast" — no threshold defined
- "The feature must be secure" — no specific rule or role named
- NFRs copied verbatim from the template with no values filled in

**Red flags (Out of Scope):**
- No Out of Scope section — leaves engineering boundaries undefined
- Out of Scope is empty when stakeholders have mentioned adjacent features during discovery

**Good examples (NFRs — specific and story-scoped):**
> - Performance: The queue must load within 2 seconds for a location with up to 2,000 pending suggestions
> - Security: Location-based data isolation must be enforced server-side; client-side filtering alone is not sufficient
> - Auditability: Every approval action must be logged with user ID, timestamp, and original suggestion values

**Good examples (Out of Scope):**
> - Bulk approval of multiple suggestions in a single action
> - Exporting the queue to CSV or Excel from this view

---

---

## Semantic Quality: The Layer Structural Evaluation Misses

The eight dimensions above evaluate **form**. A story can score 9.0 on the rubric and still be wrong — because structural evaluation checks whether the story is well-formed, not whether its content is logically sound and traceable to validated requirements.

The most dangerous quality failures are semantic. They arrive formatted correctly, pass every gate, and reach engineering carrying a false premise.

### What structural evaluation cannot catch

**Untraceable claims in AC**
An Acceptance Criteria item introduces a behavioral rule that was never stated in discovery, never listed in the epic, and never validated as an assumption. It was inferred during writing. It may be plausible — it may even be correct — but it has no source. If it's wrong, it causes a rework cycle after implementation.

Example:
> "Approved suggestions are sorted by vendor name by default."
No discovery record states a sorting preference. No stakeholder validated this. It was invented during story generation and looks completely reasonable.

**Goal-AC misalignment (sufficiency failure)**
The "so that" clause states the user goal. The AC define conditions for done. These two things can be written independently — and often are. A story fails sufficiency when all ACs could pass but the user goal would still not be achieved. This is the most dangerous failure type: the story ships, QA signs off, and no value is delivered.

**BDD-AC disconnection**
BDD scenarios and AC are often generated in separate passes. The result: scenarios that test behavior not stated in any AC (orphaned scenarios testing out-of-scope things), and AC items with no corresponding BDD scenario (conditions required but never tested). Both slip through every structural gate.

**Cross-story contradictions**
Two stories in the same epic state conflicting behavioral rules — approval thresholds, permission levels, data visibility scopes. Each story looks correct in isolation. Together they send engineering in opposite directions. No per-item evaluation catches this.

**Inferences stated as facts**
During writing, behavioral preferences ("users will want to see this sorted"), outcome predictions ("this will reduce support tickets"), and default-state inventions ("if empty, show a prompt") get embedded as requirements. They are stated as facts but are deductions. If they are wrong, the story is wrong — regardless of its structural score.

### The semantic review layer

`/backlog:semantic-review` runs five checks against the item, its parent epic, the discovery record, and all sibling stories in the same epic:

| Check | What it finds |
|---|---|
| Claim Traceability | AC items with no source in discovery, the epic, or validated assumptions |
| Goal-AC Alignment | AC sets that are unnecessary (scope creep) or insufficient (goal not achieved) |
| BDD-AC Alignment | Orphaned BDD scenarios and untested AC items |
| Cross-Story Consistency | Contradictions in rules, thresholds, and term definitions across siblings |
| Inference Audit | Claims stated as requirements that are actually unvalidated deductions |

Output is a **risk register** — not a score. Each finding includes the exact claim, why it is risky, and the question a PM or domain expert must answer to resolve it. The skill surfaces the questions; humans answer them.

### Where semantic review fits in the pipeline

```
Structural Evaluation (evaluate-item) → Semantic Review (semantic-review) → Dev-Ready Handoff (dev-ready-handoff)
```

Run semantic review on stories that scored ≥ 7.0 on the structural rubric. Stories below that threshold have form problems that should be fixed first — semantic review assumes a well-formed story.

See [workflow 07: semantic review](../workflows/07-semantic-review.md) and the [semantic review prompt](../prompts/semantic-review.md).

---

## Quick Quality Checklist

Use this before moving a story to "Ready for Sprint":

```
[ ] Story starts with "In order to" — a specific business goal, not a feature description
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
[ ] Acceptance Criteria are explicit and verifiable — they are the story's definition of done (no separate DoD section belongs in the story file)
[ ] An engineer unfamiliar with context could start work without a meeting
[ ] Story is under 150 lines — no redundant narrative sections (no separate Business Objective / Problem Context / Desired Outcome)
[ ] No Functional Requirements section — observable behaviors are expressed through AC
[ ] No shape prescription — no return types, result structures, outcome enumerations, or function signatures in any section
[ ] NFRs are project-specific with measurable thresholds — not boilerplate
[ ] Out of Scope is explicit — lists what is not included in this story
```

---

## Scoring Guide (for AI Evaluation)

When using AI to evaluate story quality, score each dimension 1–3:

| Score | Meaning |
|---|---|
| 3 | Meets the bar — no action needed |
| 2 | Partially meets — needs a specific fix |
| 1 | Does not meet — rewrite required |

**Grade interpretation:** `grade = round(total / 24 × 10, 1)`

| Grade | Status | Action |
|---|---|---|
| ≥ 9.0 | Dev-ready | Proceed to engineering handoff |
| 7.0–8.9 | Needs refinement | Apply targeted fixes — max 2 passes |
| Below 7.0 | Requires rework | Return to story generation with richer context |

See the [evaluation template](../templates/evaluation-template.md) and [evaluate-story-quality prompt](../prompts/evaluate-story-quality.md).
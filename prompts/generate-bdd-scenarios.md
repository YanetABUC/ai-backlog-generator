# Prompt: Generate BDD Scenarios

Use this prompt to generate the four required BDD scenarios for a user story. Scenarios prove that the Acceptance Criteria are met in concrete, executable situations.

---

## When to Use

- After Acceptance Criteria are written and confirmed
- When a story has AC but no scenarios
- When existing scenarios cover only the happy path
- Before handing off a story to QA for test case design

---

## Core Prompt

```
You are a senior QA engineer and business analyst writing BDD scenarios for a user story.

## Your Role

BDD scenarios translate Acceptance Criteria into concrete, executable narratives. They are not a repeat of the AC checklist — they are the proof that each criterion is met in a specific situation.

## Four Required Scenario Types

Every story requires exactly these four types:

1. **Happy Path** — the primary user action succeeds under normal conditions
2. **Validation** — invalid input or a business rule violation is correctly rejected
3. **Edge Case** — a boundary condition or unusual but valid scenario is handled correctly
4. **Failure Handling** — a system or integration failure is recovered from gracefully

## Scenario Format (Gherkin)

```gherkin
Scenario: [Descriptive name — readable by a non-technical stakeholder]
  Given [precondition — the state of the world before the user acts]
  And [additional precondition, if needed]
  When [the action the user or system performs — one action per scenario]
  Then [the observable result — specific, not "the system works"]
  And [secondary result or side effect, if applicable]
```

## Rules

**Naming:**
- Scenario names must be readable by a non-technical stakeholder
- Name describes the situation, not the expected result ("Manager approves a below-threshold suggestion" not "Success case")

**Given:**
- State what is true before the action — user context, data state, system state
- Be specific: "a pending suggestion with estimated value $3,200" not "a suggestion exists"

**When:**
- One action per scenario — if you need two, write two scenarios
- Describes what the user or system does — not what happens as a result

**Then:**
- Observable behavior only — what the user can see, read, or verify
- Specify exact error message text in quotes: `Then I see: "Approval could not be completed."`
- Never use: "the system works," "it succeeds," "the error is shown"

**Traceability:**
- Add a comment above each scenario indicating which AC items it proves: `# Proves AC1, AC3`

**Boundary conditions:**
- State threshold inclusivity explicitly: "exactly $5,000 is routed to approval" — is the threshold inclusive or exclusive?

**Data preservation:**
- In Validation and Failure Handling scenarios, always state whether data is preserved: "the form is not cleared" / "no partial state is saved"

## Input

**User story:** [paste the full story]

**Acceptance Criteria:**
[paste the AC checklist — scenarios must prove every item]

**Context:**
- Product: [brief description]
- User types: [roles in this product]
- Domain entities: [relevant data models and their states]
- Known business rules: [rules the system enforces]
- Error message conventions: [standard format your product uses, if any]

## Output

Generate the four scenarios in this order: Happy Path → Validation → Edge Case → Failure Handling.

After each group, add a traceability summary:
```
Traceability:
- AC1 → Happy Path scenario
- AC2 → Happy Path scenario
- AC3 → Validation scenario
- AC4 → Failure Handling scenario
- AC5 → Edge Case scenario
- AC6 → Edge Case scenario
```

If any AC item is not covered by a scenario, flag it explicitly.
```

---

## Variant: Generate From AC Only (No Full Story)

When you have AC but haven't written the full story narrative:

```
Generate four BDD scenarios (Happy Path, Validation, Edge Case, Failure Handling) for a feature with the following acceptance criteria.

Infer the user persona and action from the AC items. If the persona is ambiguous, state your assumption and ask for confirmation before generating.

Feature: [one-sentence description]
User: [role and context]

Acceptance Criteria:
[paste AC checklist]

Rules:
- Specify exact error message text in quotes
- State threshold inclusivity explicitly for any boundary condition
- State data preservation behavior in Validation and Failure Handling scenarios
- Add traceability comment above each scenario: # Proves AC[N]
```

---

## Variant: Fill Missing Scenario Types

When a story already has some scenarios but is missing one or more types:

```
The following story has [existing scenario types] but is missing [missing type(s)].

Generate only the missing scenario type(s).

Missing type(s) to generate: [Validation / Edge Case / Failure Handling]

Requirements for [Validation]:
- Cover: [specific invalid input or rule violation relevant to this story]
- Specify the exact error message text in quotes
- Confirm the user's data is preserved after the rejection

Requirements for [Edge Case]:
- Cover: [the specific boundary condition for this story]
- State explicitly whether the threshold is inclusive or exclusive

Requirements for [Failure Handling]:
- Cover: [the most likely system failure for this feature]
- Specify the exact recoverable error message in quotes
- Confirm no data is lost and the user can retry

Story: [paste]
Existing scenarios: [paste]
AC checklist: [paste]
```

---

## Variant: Multi-Role Scenarios

When a story has meaningfully different behavior for different user roles:

```
The following story has role-based behavior — different users see or can do different things.

Generate scenarios that explicitly cover each role's experience.

For each role, generate:
- One scenario showing what they CAN do
- One scenario showing what they CANNOT do (if applicable)

Roles and their differences:
- [Role 1]: [what they can/cannot do]
- [Role 2]: [what they can/cannot do]

Story: [paste]
AC: [paste]
```

---

## Variant: Async and Background Process Scenarios

When a story involves a background job, event, or async operation:

```
The following story involves an asynchronous or background process. Generate BDD scenarios using background-process conventions.

For async operations:
- Given/When describes the trigger event, not the user action
- Then describes the eventual observable outcome (what the user sees or receives)
- Include a scenario for delayed completion (what the user sees while waiting)
- Include a scenario for partial failure (some records processed, some failed)

Story: [paste]
AC: [paste]
Process description: [describe the background job or async behavior]
```

---

## Example Output

**Story:** Manager approves a PO suggestion without modification

**Acceptance Criteria:**
- AC1: A manager can approve a pending suggestion after reviewing a confirmation summary
- AC2: Approved suggestions under $5,000 are immediately downloadable as a PO document
- AC3: Approved suggestions at or above $5,000 are routed to procurement — not immediately downloadable
- AC4: When approval fails, suggestion status remains unchanged and the manager sees a recoverable error
- AC5: Approved suggestions move from Pending queue to Approved history
- AC6: The $5,000 threshold is inclusive

---

```gherkin
# Proves AC1, AC2, AC5
Scenario: Manager approves a below-threshold suggestion
  Given I am a warehouse manager
  And I have a pending suggestion for "Bolt M6 Stainless" from vendor "Acme Supplies"
  And the estimated PO value is $3,200
  When I click "Approve" and confirm in the confirmation dialog
  Then the suggestion status changes to "Approved"
  And a PO document is available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue
  And it appears in my Approved history tab

# Proves AC3
Scenario: Manager approves an above-threshold suggestion requiring procurement review
  Given I have a pending suggestion with estimated value $7,500
  When I confirm the approval
  Then the suggestion transitions to "Pending Procurement Approval"
  And I see: "This PO requires procurement approval before it can be downloaded."
  And no download button is shown
  And the procurement lead receives an in-app and email notification

# Proves AC6
Scenario: Approval threshold applies inclusively at exactly $5,000
  Given I have a pending suggestion with an estimated value of exactly $5,000
  When I confirm the approval
  Then the suggestion is routed to procurement approval
  And I see: "This PO requires procurement approval before it can be downloaded."
  And the suggestion is not immediately downloadable

# Proves AC4
Scenario: System error prevents the approval from completing
  Given I click "Approve" and confirm the action
  When the system returns an error before completing the transaction
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion status remains "Pending"
  And no partial approval record is created
  And my queue shows the unchanged state on the next load
```

**Traceability:**
- AC1 → Happy Path scenario
- AC2 → Happy Path scenario
- AC3 → Validation scenario (above-threshold routing)
- AC4 → Failure Handling scenario
- AC5 → Happy Path scenario
- AC6 → Edge Case scenario

**Coverage check:** All 6 AC items covered. ✅

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---|---|
| `Then the error is displayed` | `Then I see: "exact message text"` |
| `Then the system processes the request` | Describe what the user observes — not what the system does internally |
| `When the user does A and then B` | Split into two scenarios — one action per When |
| `Scenario: Success case` | Name the situation: `Scenario: Manager approves a below-threshold suggestion` |
| Boundary scenario doesn't state inclusivity | Add: "the $5,000 threshold is inclusive" or "the 30-day window excludes day 31" |
| Failure scenario doesn't confirm data preservation | Add: `And no partial state is saved` / `And the form is not cleared` |
| AC item with no covering scenario | Flag it — every AC must map to at least one scenario |

# Acceptance Criteria vs. BDD Scenarios

## The Distinction

| | Acceptance Criteria | BDD Scenarios |
|---|---|---|
| **Format** | Checklist — declarative statements | Gherkin — Given/When/Then narratives |
| **Purpose** | Define the conditions that must be true for the story to be "done" | Describe the specific behavior of the system in concrete situations |
| **Written by** | PM/BA during story creation | PM/BA during story creation, refined by QA |
| **Used by** | Anyone verifying completion | QA, developers, automated test frameworks |
| **Scope** | What must be true | How it behaves in a specific situation |

**In the canonical story template:**
- **Acceptance Criteria** = a checklist of conditions (pass/fail, deterministic, independent)
- **Scenarios (BDD)** = the four concrete situations that prove the criteria are met

---

## Acceptance Criteria Template

Acceptance criteria are **declarative statements** — not test scripts. Each one is an independent, verifiable condition.

```markdown
## Acceptance Criteria

*Each criterion must be: testable (pass/fail), deterministic (same input = same result), independent (evaluable on its own).*

- AC1: [Statement of observable system behavior — present tense, declarative]
- AC2: [Statement of observable system behavior]
- AC3: [Error or exception behavior — what the system does when something goes wrong]
- AC4: [Boundary or edge case behavior — what happens at the limit]
- AC5: [Permission or role behavior — what changes based on who is doing it]
```

### Acceptance Criteria Checklist: Coverage Guide

Before finalizing, confirm the checklist covers:

```
[ ] Primary user action succeeds — the happy path condition is stated
[ ] Data is persisted or returned correctly — observable outcome is specified
[ ] At least one validation failure — what happens with bad input
[ ] At least one system error / failure state — what the user sees when something breaks
[ ] Empty state — what the user sees when there is no data to display
[ ] Permission behavior — what authorized vs. unauthorized users experience
[ ] At least one boundary condition — behavior at the threshold (inclusive/exclusive)
```

---

## BDD Scenarios Template

BDD scenarios translate acceptance criteria into **concrete, executable narratives**. Each scenario maps to one or more AC items and proves them in a specific situation.

```markdown
## Scenarios (BDD)

### Happy Path
```gherkin
Scenario: [Descriptive name — primary success flow]
  Given [precondition — the state of the world before the user acts]
  And [additional precondition if needed]
  When [the user performs the action]
  Then [the expected result — specific and observable]
  And [secondary result or side effect]
```

### Validation
```gherkin
Scenario: [Name — what invalid input or rule is being enforced]
  Given [user is attempting the action]
  When [invalid input is provided or a rule is violated]
  Then [the system rejects it with: "exact message text"]
  And [user's data is preserved / form is not cleared]
```

### Edge Case
```gherkin
Scenario: [Name — boundary condition or unusual but valid scenario]
  Given [the boundary condition exists]
  When [the user acts]
  Then [the system handles it correctly — specify exact behavior, including threshold inclusivity]
```

### Failure Handling
```gherkin
Scenario: [Name — system or integration failure]
  Given [the user is performing the action]
  When [a system failure occurs — API error, timeout, service unavailable]
  Then [the user sees: "exact recoverable error message"]
  And [no data is lost / the user can retry without re-entering their work]
```
```

---

## Writing Good Acceptance Criteria (Checklist Style)

### Do

- Write in present tense: "A manager viewing the queue sees..."
- Be specific: "The form shows: 'Please select a refund reason'" not "shows an error"
- State the full condition: what precondition + what behavior
- Keep each criterion independent — one thing per bullet

### Don't

- Use subjective language: "works as expected," "performs well," "looks correct"
- Combine two conditions in one bullet (split them)
- Describe implementation: "the API returns 200" — write "the record is saved and confirmation is shown"
- Repeat the scenario in the checklist — AC is the condition, BDD is the story of it

---

## Example: Acceptance Criteria + BDD Scenarios for the Same Story

**Story:** Manager approves a PO suggestion

### Acceptance Criteria (Checklist)

- AC1: A manager can approve a pending suggestion after reviewing a confirmation summary showing vendor name, quantity, and estimated value
- AC2: Approved suggestions with value under $5,000 are immediately available for download as a PO document
- AC3: Approved suggestions with value at or above $5,000 are routed to the procurement lead — not immediately downloadable
- AC4: When approval fails due to a system error, the suggestion status remains unchanged and the manager sees a recoverable error message
- AC5: An approved suggestion is removed from the Pending queue and appears in the Approved history
- AC6: The $5,000 threshold is inclusive — a suggestion valued at exactly $5,000 is routed to procurement approval

### Scenarios (BDD)

```gherkin
# Proves AC1, AC2, AC5
Scenario: Manager approves a low-value suggestion
  Given I have a pending suggestion for vendor "Acme" with estimated value $3,200
  When I click "Approve" and confirm the action
  Then the suggestion status changes to "Approved"
  And the PO document is available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue

# Proves AC3
Scenario: Manager approves a high-value suggestion
  Given I have a pending suggestion with estimated value $7,500
  When I confirm approval
  Then the suggestion transitions to "Pending Procurement Approval"
  And the procurement lead receives a notification
  And I see: "This PO requires procurement approval before it can be downloaded."

# Proves AC6 (boundary)
Scenario: Approval threshold is inclusive at exactly $5,000
  Given I approve a suggestion with estimated value of exactly $5,000
  When the approval is confirmed
  Then the suggestion is routed to procurement approval — not immediately downloadable

# Proves AC4
Scenario: API failure during approval
  Given I confirm the approval action
  When the system returns an error before completing
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion remains in Pending status
```

---

## Key Rule

**Acceptance Criteria = the checklist the story must pass.**
**BDD Scenarios = the proof that it passed.**

Every AC item should be traceable to at least one BDD Scenario. Every BDD Scenario should trace to at least one AC item.

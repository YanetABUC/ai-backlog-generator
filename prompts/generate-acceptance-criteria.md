# Prompt: Generate Acceptance Criteria and BDD Scenarios

Use this prompt to generate the two complementary quality sections of a user story:
- **Acceptance Criteria** — a checklist of conditions the story must satisfy
- **BDD Scenarios** — concrete Given/When/Then narratives that prove the criteria are met

These are distinct and both required. Do not conflate them.

---

## When to Use

- After writing a user story narrative, before the story is considered complete
- When refining AI-generated stories that have vague or missing quality sections
- As a standalone quality pass before sprint planning

---

## Core Prompt

```
You are a senior business analyst and QA lead generating the Acceptance Criteria and BDD Scenarios for a user story.

## The Distinction

**Acceptance Criteria** = a checklist of declarative conditions that must be true for the story to be "done."
- Format: bullet list of present-tense statements
- Each item is: testable (pass/fail), deterministic, independent
- No Given/When/Then — those are scenarios

**BDD Scenarios** = concrete narratives that prove the criteria are met in specific situations.
- Format: Gherkin (Given/When/Then)
- Four required types: Happy Path, Validation, Edge Case, Failure Handling
- Each scenario maps to one or more AC items

## Acceptance Criteria Requirements

Generate 5–8 AC items covering:
- [ ] Primary action succeeds — the happy path condition
- [ ] Data persisted or returned — observable outcome specified
- [ ] At least one validation failure — bad input behavior
- [ ] At least one system/integration failure — what the user sees
- [ ] Empty state — no data to display
- [ ] Permission behavior — authorized vs. unauthorized
- [ ] At least one boundary condition — threshold behavior (state if inclusive/exclusive)

Format:
- AC1: [declarative statement of observable behavior]
- AC2: [declarative statement]
- (no Given/When/Then — that is a scenario)

## BDD Scenarios Requirements

Generate exactly four scenarios:

### Happy Path
- Covers the primary success flow
- Proves 1–3 AC items
- Specifies observable results — not system internals

### Validation
- Covers invalid input or business rule violation
- Specifies the exact error message text in quotes
- States that data is preserved / not lost

### Edge Case
- Covers a boundary condition or unusual but valid scenario
- States threshold behavior explicitly (inclusive or exclusive)

### Failure Handling
- Covers a system or integration failure (API error, timeout, service unavailable)
- Specifies the exact error message in quotes
- States that no data is lost and the user can retry

## Traceability

After generating, add a comment on each scenario indicating which AC items it proves:
`# Proves AC1, AC3`

## User Story

[paste the user story narrative here]

## Context

**Product:** [brief product description]
**User types:** [list of roles in this product]
**Domain entities:** [relevant data models and their states]
**Known business rules:** [rules the system already enforces]
**Error message conventions:** [if your product has a standard format, specify it]
```

---

## Variant: AC Checklist Only (Fast Pass)

When you need criteria quickly and will write scenarios manually:

```
Generate Acceptance Criteria (checklist only — no BDD scenarios) for the following user story.

Format: declarative bullet list. No Given/When/Then.

Each item must be:
- Pass/fail testable
- Deterministic (same input = same result)
- Independent (evaluable without reading other items)

Cover: happy path, validation failure, system failure, empty state, permission behavior, boundary condition.

Story: [paste]
Context: [paste]
```

---

## Variant: BDD Scenarios Only (From Existing AC)

When AC already exists and you need to prove it with scenarios:

```
The following user story has Acceptance Criteria. Generate BDD Scenarios that prove each AC item is met.

Requirements:
- Generate exactly four scenario types: Happy Path, Validation, Edge Case, Failure Handling
- Each scenario must map to at least one AC item (annotate with "# Proves AC1, AC2")
- Specify exact error message text in quotes — do not write "shows an error"
- State threshold behavior explicitly in edge case scenarios (inclusive or exclusive)
- State data preservation behavior in all failure and validation scenarios

Story: [paste]
Acceptance Criteria: [paste]
Context: [paste]
```

---

## Variant: Add Missing Failure Handling Scenario

When a story has scenarios but is missing the failure case:

```
The following story has Happy Path, Validation, and Edge Case scenarios but is missing a Failure Handling scenario.

Generate a Failure Handling scenario covering:
- The most likely system failure for this feature (API error, timeout, third-party service unavailable)
- The exact error message the user sees (in quotes)
- Confirmation that no data is lost
- Confirmation that the user can retry without re-entering their work

Story: [paste]
Existing scenarios: [paste]
Context: [paste]
```

---

## Example Output

**Story:** Manager approves a PO suggestion without modification

### Acceptance Criteria

- AC1: A manager can approve a pending suggestion after reviewing a confirmation summary showing vendor name, quantity, and estimated value
- AC2: Approved suggestions valued under $5,000 are immediately available for download as a PO document — no additional approval required
- AC3: Approved suggestions valued at or above $5,000 are routed to the procurement lead for review — the manager sees a status message, not a download button
- AC4: When approval fails due to a system error, the suggestion status remains "Pending" and the manager sees a recoverable error message
- AC5: An approved suggestion is removed from the Pending queue and appears in the Approved history tab
- AC6: The $5,000 routing threshold is inclusive — a suggestion valued at exactly $5,000 is routed to procurement approval

---

### Scenarios (BDD)

```gherkin
# Proves AC1, AC2, AC5
Scenario: Manager approves a below-threshold suggestion
  Given I have a pending suggestion for vendor "Acme Supplies" with estimated value $3,200
  When I click "Approve" and confirm the action in the confirmation dialog
  Then the suggestion status changes to "Approved"
  And a PO document is available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue

# Proves AC3
Scenario: Manager approves an above-threshold suggestion
  Given I have a pending suggestion with estimated value $7,500
  When I confirm the approval
  Then the suggestion transitions to "Pending Procurement Approval"
  And the procurement lead receives an in-app and email notification
  And I see: "This PO requires procurement approval before it can be downloaded."

# Proves AC6
Scenario: Approval threshold is inclusive at exactly $5,000
  Given I have a pending suggestion with an estimated value of exactly $5,000
  When I confirm the approval
  Then the suggestion is routed to procurement approval — not immediately downloadable
  And I see: "This PO requires procurement approval before it can be downloaded."

# Proves AC4
Scenario: API failure during the approval action
  Given I click Approve and confirm the action
  When the system returns an error before completing the transaction
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion status remains "Pending" — no partial state is saved
  And my queue reflects the unchanged state on the next load
```

---

## Writing Rules

**For Acceptance Criteria:**
- ✅ "A manager can approve a pending suggestion after reviewing a summary" — declarative, observable
- ✅ "Approved suggestions valued under $5,000 are immediately downloadable" — specific condition
- ❌ "The system works correctly" — not testable
- ❌ "Given the user clicks Approve, when..." — that's a scenario, not an AC item
- ❌ "The API returns a 200 status" — implementation detail, not observable behavior

**For BDD Scenarios:**
- ✅ Specify exact error message text: `Then I see: "Approval could not be completed. Please try again."`
- ✅ State threshold inclusivity: "exactly $5,000 is routed to procurement approval"
- ✅ Confirm data preservation: "no partial state is saved"
- ❌ "Then the system handles the error" — not specific enough
- ❌ "Then an error appears" — what error? What text?

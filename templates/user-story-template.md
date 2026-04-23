# User Story Template

Use this template for every user story. Fill every section before marking a story Ready for Sprint.

**Naming convention:** `US{number}-{kebab-case-title}.md` (e.g. `US3-manager-approves-purchase-order.md`)

---

```markdown
# US{N} — [Short Title]

**Epic:** [epic name / link]
**Status:** Draft / Ready / In Progress / Done
**Size:** S / M / L
**Sprint:** [sprint number or TBD]

---

## User Story

**In order to** [business goal or outcome the organization/team achieves],
**As a** [specific persona — role + context, never just "user"],
**I want to** [the capability or action the user performs],
**So that** [the direct benefit to the user — measurable, not circular].

---

## Business Objective

[One or two sentences connecting this story to a measurable business goal, OKR, or strategic initiative. Why does the business care about shipping this?]

---

## Problem Context

**Current situation:** [How does the user accomplish this today? What tool, workaround, or manual process exists?]

**Pain points:**
- [Pain point 1 — specific and observable]
- [Pain point 2]
- [Pain point 3]

**Operational risks of the current state:**
- [Risk if we don't solve this — e.g., data loss, revenue impact, compliance gap]

**Constraints:**
- [Constraint 1 — regulatory, technical, organizational, or time-based]
- [Constraint 2]

---

## Desired Outcome

**User impact:** [What changes about the user's experience or workflow when this story ships?]

**Business impact:** [What measurable business outcome follows from the user impact?]

**Success metrics:**
| Metric | Baseline | Target |
|---|---|---|
| [metric 1] | [current] | [goal] |
| [metric 2] | | |

---

## Acceptance Criteria

*Each criterion must be: testable (pass/fail), deterministic (same input = same result), and independent (evaluable on its own).*

- AC1: [Criterion — written as a declarative statement of observable behavior]
- AC2: [Criterion]
- AC3: [Criterion]
- AC4: [Criterion — error state]
- AC5: [Criterion — edge case or boundary condition]

---

## Scenarios (BDD)

### Happy Path

```gherkin
Scenario: [Descriptive name for the primary success flow]
  Given [the user is in the correct context / precondition is true]
  And [additional precondition if needed]
  When [the user performs the primary action]
  Then [the expected result occurs — specific and observable]
  And [secondary result or side effect]
```

### Validation

```gherkin
Scenario: [Name — what invalid input or rule is being validated]
  Given [user is attempting the action]
  When [invalid input is submitted or a rule is violated]
  Then [the system rejects the input with a specific message: "exact message text"]
  And [the user's data is preserved / the form is not cleared]
```

### Edge Case

```gherkin
Scenario: [Name — boundary condition or unusual but valid scenario]
  Given [the boundary condition exists]
  When [the user performs the action]
  Then [the system handles the boundary correctly — specify the exact behavior]
```

### Failure Handling

```gherkin
Scenario: [Name — system or integration failure]
  Given [the user is performing the action]
  When [a system failure occurs — e.g., API error, timeout, service unavailable]
  Then [the user sees a recoverable error: "exact message text"]
  And [no data is lost / the user can retry without re-entering their work]
```

---

## Functional Requirements

*Observable behaviors the system must exhibit. What, not how.*

- **FR1:** [The system must... — describe observable behavior]
- **FR2:** [The system must...]
- **FR3:** [The system must...]
- **FR4:** [The system must... — error or validation behavior]
- **FR5:** [The system must... — edge case behavior]

---

## Non-Functional Requirements

**Performance:**
- [e.g., The feature must respond within 2 seconds under normal load for up to N concurrent users]

**Security:**
- [e.g., Only users with the [role] permission may access this feature]
- [e.g., All user input must be sanitized before storage and display]

**Reliability:**
- [e.g., The feature must remain available during partial downstream service failures]

**Auditability:**
- [e.g., Every state change must be logged with user ID, timestamp, and before/after values]

**Scalability:**
- [e.g., The feature must perform within SLA with up to N records in the dataset]

---

## Out of Scope

*Be explicit. Prevents scope creep and sets clear engineering boundaries.*

- [Item explicitly excluded — e.g., "Bulk approval of multiple suggestions in one action"]
- [Item explicitly excluded]
- [Item explicitly excluded]

---

## Media

*Links to design assets, prototypes, or supporting research.*

- **Design:** [Figma link or "TBD"]
- **Prototype:** [link or "N/A"]
- **Research / context:** [link to user research, analytics report, or support data — or "N/A"]
```

---

## What This Template Prohibits

The following sections must **never** appear in a user story:

| Prohibited section | Why |
|---|---|
| Implementation Notes / Files to modify | Prescribes *how* — removes engineering autonomy |
| Technical Design / Architecture decisions | Engineering concern, belongs in ADRs or design docs |
| Pseudocode / code blocks | Prescribes *how* — stories describe *what* and *why* |
| API specifications | Belongs in the technical spec, linked from the epic |
| Database schema changes | Technical story or ADR, not a user story |

All system behavior belongs in: Acceptance Criteria, BDD Scenarios, Functional Requirements, or Non-Functional Requirements — expressed in plain English.

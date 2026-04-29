# User Story Template

Use this template for every user story. Fill every section before marking a story Ready for Sprint.

**Naming convention:** `US{number}-{kebab-case-title}.md` (e.g. `US3-manager-approves-purchase-order.md`)
**Target length:** 80–150 lines. If a story exceeds 200 lines, review for redundancy before marking it ready.

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

> **Ownership boundary:** This story defines *what behavior* the system must exhibit. Implementation decisions — data structures, return types, function signatures, error handling patterns — are owned by the development team.

---

## Context

[2–5 sentences covering: the current situation, the pain point or gap, and what changes when this story ships. Do not repeat the User Story clauses. Do not break this into sub-sections — one paragraph only.]

---

## Acceptance Criteria

*Each criterion must be: testable (pass/fail), deterministic (same input = same result), and independent (evaluable on its own). Numbered list. No Given/When/Then here — that belongs in Scenarios.*

1. [Criterion — observable behavior, pass/fail]
2. [Criterion]
3. [Criterion — error state]
4. [Criterion — edge case or boundary condition]

---

## Scenarios (BDD)

*Include only when the AC cannot fully express a complex multi-step interaction or sequence. If the AC is sufficient, omit this section entirely. Maximum 5 scenarios.*

### Happy Path

```gherkin
Scenario: [Descriptive name for the primary success flow]
  Given [the user is in the correct context / precondition is true]
  And [additional precondition if needed]
  When [the user performs the primary action]
  Then [the expected result — specific and observable]
  And [secondary result or side effect]
```

### Validation

```gherkin
Scenario: [Name — what invalid input or rule is being validated]
  Given [user is attempting the action]
  When [invalid input is submitted or a rule is violated]
  Then [the system rejects with a specific message: "exact message text"]
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

## Non-Functional Requirements

*Only include NFRs **specific to this story**. Do not repeat project-wide standards (performance baselines, logging conventions, etc.). If no story-specific NFRs apply, omit this section entirely.*

**Performance:** [specific threshold — response time, concurrency, or volume that differs from baseline for this story]
**Security:** [specific access rule — which roles, what is enforced server-side]
**Reliability:** [specific degraded-mode or partial-failure behavior]
**Auditability:** [what is logged, with which fields — specific to this story]
**Scalability:** [concrete volume or load target for this story]

---

## Out of Scope

*Be explicit. Prevents scope creep and sets clear engineering boundaries.*

- [Item explicitly excluded — e.g., "Bulk approval of multiple suggestions in one action"]
- [Item explicitly excluded]

---

## Dependencies

- [ ] [dependency — e.g., "Design: Figma frame #123 approved"]
- [ ] [dependency — e.g., "Backend: /api/inventory/locations endpoint available"]

---

## Media

- **Design:** [Figma link or "TBD"]
- **Prototype:** [link or "N/A"]
- **Research / context:** [link or "N/A"]
```

---

## What This Template Prohibits

The following must **never** appear in a user story:

| Prohibited | Why |
|---|---|
| Separate Business Objective / Problem Context / Desired Outcome sections | Redundant — all three collapse into the Context paragraph |
| Functional Requirements (FR1, FR2...) section | Duplicates AC and causes shape prescription — observable behaviors belong in AC |
| Shape prescription — return types, result structures, outcome enumerations, function signatures | AI coding assistants build exactly what stories describe; prescribing shape produces the wrong shape |
| Implementation Notes / Files to modify | Prescribes *how* — removes engineering autonomy |
| Technical Design / Architecture decisions | Engineering concern, belongs in ADRs or design docs |
| Pseudocode / code blocks | Prescribes *how* — stories describe *what* and *why* |
| API specifications, database schema changes | Technical spec or ADR, not a user story |

**Shape prescription** is any statement that could be translated into a type definition or interface contract by an AI coding assistant: "return one of X / Y / Z," "the result must include fields A, B, C," "accept parameters X and Y." If a sentence defines what data looks like or how a component is structured, it belongs in the technical spec — not here.

All system behavior belongs in Acceptance Criteria and (optionally) BDD Scenarios, expressed in plain English as observable behavior.

# Prompt: Generate User Stories

Use this prompt to generate dev-ready user stories from an epic definition. Output follows the project's canonical user story template.

---

## When to Use

- After epics are defined and prioritized
- When breaking an epic into sprint-sized stories
- When generating stories from a prototype or codebase analysis

---

## Core Prompt

```
You are a senior business analyst generating user stories for a software product.

Generate a complete set of user stories for the following epic. Stories must together cover the full scope — including happy path, alternative flows, error states, and edge cases.

## Story Format

Every story must follow this exact structure. Target 80–150 lines per story.

---
# US{N} — [Short Title]

## User Story
**In order to** [business goal the organization achieves],
**As a** [specific persona — role + context, never just "user"],
**I want to** [the capability or action],
**So that** [direct, measurable benefit to the user].

> **Ownership boundary:** This story defines *what behavior* the system must exhibit. Implementation decisions — data structures, return types, function signatures, error handling patterns — are owned by the development team.

## Context
[2–5 sentences: the current situation, the pain point or gap, and what changes when this story ships. One paragraph only — do not break into sub-sections.]

## Acceptance Criteria
*Numbered list. Each criterion: pass/fail testable, deterministic, independent. No Given/When/Then here — that belongs in Scenarios.*
1. [observable behavior — pass/fail]
2. [observable behavior]
3. [error state behavior]
4. [edge case or boundary condition]

## Scenarios (BDD)
*Include only when AC cannot fully express a complex multi-step interaction. Omit if AC is sufficient. Maximum 5 scenarios.*

### Happy Path
Given / When / Then

### Validation
Given / When / Then — with exact quoted error message text

### Edge Case
Given / When / Then — boundary condition, specify inclusive/exclusive

### Failure Handling
Given / When / Then — system failure, confirm data is preserved and user can retry

## Non-Functional Requirements
*Story-specific only. Omit if no NFRs specific to this story apply. Do not repeat project-wide standards.*
**Performance:** [specific threshold]
**Security:** [specific role or rule]
**Reliability:** [specific degraded-mode behavior]
**Auditability:** [what is logged, with which fields]
**Scalability:** [concrete volume or load target]

## Out of Scope
- [explicit exclusion]
- [explicit exclusion]

## Media
- Design: [Figma link or TBD]
- Prototype: [link or N/A]
---

## CRITICAL CONSTRAINTS

- Never include: Business Objective / Problem Context / Desired Outcome as separate sections — that content goes in the Context paragraph
- Never include: Functional Requirements (FR1, FR2...) — observable behaviors belong in AC
- Never include: Implementation Notes, Technical Design, Pseudocode, file paths, API specs, database schemas
- Never prescribe shape: no return type enumerations, no result object structures, no function signatures — if a sentence could become a type definition, rewrite it as behavior
- Stories describe WHAT and WHY — never HOW
- "So that" must be measurable — not "so that it works" or "so that I can do things"
- Never use "user" as a persona — always role + context
- Error messages in scenarios must be exact quoted text, not "shows an error"
- Scenarios are optional — only include them when AC cannot express the behavior alone

## Epic Context

**Epic title:** [title]
**User segment:** [who this is for — specific]
**Business goal:** [measurable outcome]
**Constraints:** [technical, time, or regulatory]
**Out of scope for this epic:** [explicitly excluded]
**Existing capabilities to reference (not re-specify):** [list if applicable]

## Coverage Requirements

Generate stories that collectively cover:
- [ ] Primary happy path
- [ ] Alternative user paths / choices
- [ ] Empty state (when no data is available)
- [ ] Error states (system failures, validation errors, permission denied)
- [ ] Permission variations (different roles, partial access)
- [ ] Boundary/edge conditions
- [ ] Mobile/responsive behavior (if UI feature)

Generate [5–8] stories.
```

---

## Variant: Stories From Prototype Screens

```
Generate user stories from the following prototype screen analysis.

Use the canonical story format (In order to / As a / I want to / So that + Context + AC + optional Scenarios + NFRs + Out of Scope).

For each screen or user interaction:
- Reference the specific screen by name in the Context paragraph
- Cover all UI states (empty, loading, error, success) as AC items; use Scenarios only for complex multi-step interactions
- Express implied backend behaviors as AC items — in plain English, not API specs or FR lists
- Link the Figma frame in the Media section

Screen analysis:
[paste output from Workflow 02, Steps 2–4]
```

---

## Variant: Stories From Codebase Gap Analysis

```
Generate user stories for the following identified gaps.

Use the canonical story format (In order to / As a / I want to / So that + Context + AC + optional Scenarios + NFRs + Out of Scope).

For each gap:
- Reference the relevant domain entities by business name (not technical identifiers) in the Context paragraph
- Express all system behaviors as AC items — not as Functional Requirements, code, or API specs
- Note existing capabilities this story extends in the Context paragraph

Identified gaps:
[paste from Workflow 03, Step 4]

Domain model (business terms):
[paste from Workflow 03, Step 2]

Business rules:
[paste from Workflow 03, Step 5]

UX gaps and missing scenarios:
[paste from Workflow 03, Step 5]
```

---

## Variant: Quick Story Generation (Lightweight)

For initial drafts that will be refined in a follow-up pass:

```
Generate [N] user stories for [feature name] targeting [user type].

Use the canonical story format (all sections required).
Minimum per story: specific persona, measurable "so that," Context paragraph (2–5 sentences), 4 numbered AC items, Failure Handling scenario. No separate FR section.

Context: [2–3 sentences about the product and feature]
```

---

## Example Output

```markdown
# US3 — Manager approves a purchase order suggestion

## User Story
**In order to** reduce the time between stock alert and vendor order submission,
**As a** warehouse manager reviewing my daily purchase order suggestion queue,
**I want to** approve a purchase order suggestion in a single action,
**So that** a PO document is available for download within seconds — without manual data entry.

> **Ownership boundary:** This story defines *what behavior* the system must exhibit. Implementation decisions — data structures, return types, function signatures, error handling patterns — are owned by the development team.

## Context
Managers currently export a stock report, filter it in Excel, and manually create POs in the vendor portal — one vendor at a time, 8–15 minutes each. There is no audit trail and no fallback when a manager is absent. This story replaces that process with a single-action approval flow that generates a downloadable PO document immediately.

## Acceptance Criteria
1. A manager viewing a pending suggestion can approve it via a single confirm action after reviewing a summary showing vendor name, quantity, and estimated value
2. When the approved PO value is under $5,000, the PO document is immediately available for download — no additional approval step required
3. When the approved PO value is $5,000 or more, the PO transitions to "Pending Procurement Approval" and the procurement lead is notified
4. When the approval action fails due to a system error, the suggestion status does not change and the manager sees a recoverable error message
5. An approved suggestion is removed from the Pending queue and appears in the Approved history
6. Submitting the approval action twice results in exactly one PO being created

## Scenarios (BDD)

### Happy Path
```gherkin
Scenario: Manager approves a low-value PO suggestion
  Given I am a warehouse manager with a pending PO suggestion in my queue
  And the suggestion's estimated value is under $5,000
  When I click "Approve" and confirm the action
  Then the suggestion status changes to "Approved"
  And a PO document is generated and available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue
```

### Validation
```gherkin
Scenario: Manager attempts to approve a suggestion already actioned by another session
  Given a suggestion has been dismissed by another manager session
  When I attempt to approve it
  Then I see: "This suggestion is no longer available. It may have been approved or dismissed."
  And I am returned to the Pending queue
```

### Edge Case
```gherkin
Scenario: PO value is exactly $5,000
  Given I approve a suggestion with an estimated value of exactly $5,000
  When the approval is confirmed
  Then the PO is routed to procurement approval — the $5,000 threshold is inclusive
  And I see: "This PO requires procurement approval before it can be downloaded."
```

### Failure Handling
```gherkin
Scenario: API failure during approval
  Given I confirm the approval action
  When the system returns an error before completing the transaction
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion remains in Pending status — no partial state is saved
```

## Non-Functional Requirements
**Performance:** The approval action must complete within 3 seconds under normal load; document generation within 10 seconds.
**Security:** Only users with the Warehouse Manager or Procurement Lead role may approve suggestions; access is restricted to their assigned location.
**Reliability:** If document generation fails, the approval is still recorded and the document queued — the manager is notified when it becomes available.
**Auditability:** Every approval action must be logged with actor, timestamp, original suggestion values, and resulting PO status.

## Out of Scope
- Bulk approval of multiple suggestions in a single action
- Modifying the suggested vendor or quantity during the approval flow (see US4)
- Direct API submission to vendor portals (Phase 2)
- Automated approval without manager review

## Media
- Design: [Figma frame — PO Suggestion Queue > Approve flow]
- Prototype: [link or TBD]
- Research: [Replenishment time study — Q1 2025]
```

---

## Tips for Better Output

- Paste your domain model (entity names, statuses, business rules) into the prompt — it prevents hallucinated behavior
- If output is too generic, add user research quotes or analytics data to the Problem Context section
- For epics with 10+ stories, generate in two passes: happy paths first, then error/edge stories
- Run the output through [evaluate-story-quality.md](evaluate-story-quality.md) before accepting it

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

Every story must follow this exact structure:

---
# US{N} — [Short Title]

## User Story
**In order to** [business goal the organization achieves],
**As a** [specific persona — role + context, never just "user"],
**I want to** [the capability or action],
**So that** [direct, measurable benefit to the user].

## Business Objective
[One sentence connecting this story to a measurable business goal or OKR.]

## Problem Context
**Current situation:** [how the user does this today]
**Pain points:**
- [pain point 1]
**Operational risks:** [what goes wrong if we don't solve this]
**Constraints:** [regulatory, technical, time-based]

## Desired Outcome
**User impact:** [what changes about their workflow]
**Business impact:** [measurable outcome]
**Success metrics:** [metric | baseline | target]

## Acceptance Criteria
*Each criterion: testable (pass/fail), deterministic, independent.*
- AC1: [observable behavior]
- AC2: [observable behavior]
- AC3: [error state behavior]
- AC4: [edge case behavior]

## Scenarios (BDD)

### Happy Path
Given / When / Then

### Validation
Given / When / Then — with exact error message text

### Edge Case
Given / When / Then — boundary condition

### Failure Handling
Given / When / Then — system failure with recovery path

## Functional Requirements
- FR1: The system must [observable behavior]
- FR2: The system must [observable behavior]
- FR3: The system must [error/validation behavior]

## Non-Functional Requirements
**Performance:** [response time, load, throughput]
**Security:** [access control, input handling]
**Reliability:** [behavior under partial failure]
**Auditability:** [logging and traceability]
**Scalability:** [data volume, concurrency]

## Out of Scope
- [explicit exclusion]
- [explicit exclusion]

## Media
- Design: [Figma link or TBD]
- Prototype: [link or N/A]
---

## CRITICAL CONSTRAINTS

- Never include: Implementation Notes, Technical Design, Pseudocode, file paths, API specs, database schemas
- Stories describe WHAT and WHY — never HOW
- "So that" must be measurable — not "so that it works" or "so that I can do things"
- Never use "user" as a persona — always role + context
- Error messages in scenarios must be exact quoted text, not "shows an error"
- Every story must have at least one Failure Handling scenario

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

Use the canonical story format (In order to / As a / I want to / So that + all sections).

For each screen or user interaction:
- Reference the specific screen by name in the Problem Context
- Include all UI states (empty, loading, error, success) as BDD Scenarios
- Express implied backend behaviors as Functional Requirements — in plain English, not technical specs
- Link the Figma frame in the Media section

Screen analysis:
[paste output from Workflow 02, Steps 2–4]
```

---

## Variant: Stories From Codebase Gap Analysis

```
Generate user stories for the following identified gaps.

Use the canonical story format (In order to / As a / I want to / So that + all sections).

For each gap:
- Reference the relevant domain entities by business name (not technical identifiers) in the Problem Context
- Express system behaviors as Functional Requirements and BDD Scenarios — not as code or API specs
- Note existing capabilities this story extends in the "Current situation" of Problem Context

Identified gaps:
[paste from Workflow 03, Step 4]

Domain model (business terms):
[paste from Workflow 03, Step 2]

Business rules:
[paste from Workflow 03, Step 5]
```

---

## Variant: Quick Story Generation (Lightweight)

For initial drafts that will be refined in a follow-up pass:

```
Generate [N] user stories for [feature name] targeting [user type].

Use the canonical story format (all sections required).
Minimum per story: specific persona, measurable "so that," 4 AC, Happy Path + Failure Handling scenarios, 3 FRs.

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

## Business Objective
Reduce average PO creation time from 8–15 minutes per vendor to under 2 minutes, contributing to the goal of 80% reduction in manual replenishment effort within 60 days of launch.

## Problem Context
**Current situation:** Managers export a stock report, identify low-inventory items in Excel, and manually create POs in the vendor portal — one vendor at a time.
**Pain points:**
- Each PO takes 8–15 minutes to create manually
- The process is repeated daily for each vendor with low-stock SKUs
- No audit trail exists for who created the PO or when

**Operational risks:** Stockouts occur when managers are absent or overwhelmed; no fallback process exists.
**Constraints:** POs over $5,000 require procurement lead approval before submission to the vendor.

## Desired Outcome
**User impact:** Managers approve a pre-populated PO in under 30 seconds and immediately receive a downloadable document — no portal navigation, no manual entry.
**Business impact:** Manual PO creation effort drops to under 20% of current volume within 30 days of launch.
**Success metrics:**
| Metric | Baseline | Target |
|---|---|---|
| Time to create one PO | 8–15 min | < 2 min |
| % POs with full audit trail | 0% | 100% |

## Acceptance Criteria
- AC1: A manager viewing a pending suggestion can approve it via a single confirm action after reviewing a summary of vendor, quantity, and estimated value
- AC2: When the approved PO value is under $5,000, the PO document is immediately available for download — no additional approval step is required
- AC3: When the approved PO value is $5,000 or more, the PO transitions to "Pending Procurement Approval" and the procurement lead is notified
- AC4: When the approval action fails due to a system error, the suggestion status does not change and the manager is shown a recoverable error message
- AC5: An approved suggestion is removed from the Pending queue and appears in the Approved history

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

## Functional Requirements
- FR1: The system must display a confirmation summary showing vendor name, total quantity, and estimated value before the manager commits to approval
- FR2: The system must route any approved PO with a value at or above the configured approval threshold to the procurement lead for review
- FR3: The system must generate a downloadable PO document within 10 seconds of a completed approval for POs below the approval threshold
- FR4: The system must log every approval action with: user ID, timestamp, original suggested values, and any modifications made prior to approval
- FR5: The system must prevent duplicate approvals — submitting the approval action twice must result in exactly one PO being created

## Non-Functional Requirements
**Performance:** The approval action must complete within 3 seconds under normal load; document generation must complete within 10 seconds.
**Security:** Only users with the Warehouse Manager or Procurement Lead role may approve suggestions; access is restricted to suggestions within their assigned location.
**Reliability:** If the document generation service is unavailable, the approval must still be recorded and the document queued for generation — the manager must be notified when it becomes available.
**Auditability:** Every state transition on a PO suggestion must be immutably logged with actor, timestamp, and changed values.
**Scalability:** The approval flow must perform within SLA with up to 500 pending suggestions across all locations.

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

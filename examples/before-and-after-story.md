# Before and After: Story Transformation

This document shows the complete transformation of a poor-quality story into a dev-ready story using the canonical lean template.

---

## The Original Story (First Draft)

```
As a user, I want to approve purchase order suggestions so that POs get created.
```

**Score:** 9/24 — Requires Rework

**Issues:**
- "user" — no role, no context; story doesn't start with "In order to"
- "so that POs get created" — system action, not a user benefit
- No Context, no Acceptance Criteria, no Scenarios
- No NFRs, no Out of Scope

---

## The Transformation, Step by Step

### Fix 1: Rewrite the narrative with the correct format

```
Before:
As a user, I want to approve purchase order suggestions so that POs get created.

After:
In order to reduce the time between stock alert and vendor order submission,
As a warehouse manager reviewing my daily purchase order suggestion queue,
I want to approve a purchase order suggestion in a single action,
So that a PO document is available for download within seconds — without manual data entry.
```

**Why it matters:** The "In order to" ties the story to the business goal. The persona tells engineers who can access this feature (preventing privilege bugs). The "So that" sets the UX success bar — the document must be immediately available, meaning the queue must clear after approval.

Add the ownership boundary statement directly after the User Story:

> **Ownership boundary:** This story defines *what behavior* the system must exhibit. Implementation decisions — data structures, return types, function signatures, error handling patterns — are owned by the development team.

---

### Fix 2: Replace three sections with one Context paragraph

Three separate sections — Business Objective, Problem Context, and Desired Outcome — say the same thing from three angles. Collapse them into a single paragraph. Shorter stories give AI coding assistants a cleaner signal.

```
Before:
## Business Objective
Reduce average PO creation time from 8–15 minutes per vendor to under 2 minutes...

## Problem Context
Current situation: Managers export a stock report...
Pain points:
- Each PO takes 8–15 minutes to create manually
...

## Desired Outcome
User impact: Managers approve a pre-populated PO in under 30 seconds...
Business impact: Manual PO creation effort drops to under 20%...
Success metrics: [table]

After (one paragraph):
## Context
Managers currently export a stock report, filter it in Excel, and manually create POs in
the vendor portal — one vendor at a time, 8–15 minutes each. There is no audit trail and
no fallback when a manager is absent. This story replaces that process with a single-action
approval flow that generates a downloadable PO document immediately.
```

---

### Fix 3: Add numbered, testable Acceptance Criteria

```
Before: (none)

After:
1. A manager viewing a pending suggestion can approve it via a single confirm action
   after reviewing a summary showing vendor name, quantity, and estimated value
2. When the approved PO value is under $5,000, the document is immediately available
   for download — no additional approval step required
3. When the approved PO value is $5,000 or more, the PO transitions to
   "Pending Procurement Approval" and the procurement lead is notified
4. When the approval action fails, the suggestion status does not change and
   the manager sees a recoverable error message
5. An approved suggestion is removed from the Pending queue
6. Submitting the approval action twice results in exactly one PO being created
```

Each criterion is numbered and pass/fail. A QA engineer can test each independently. Notice that AC6 (duplicate prevention) replaces what used to be FR5 — the behavior is expressed as an observable condition, not a system obligation in a separate section.

---

### Fix 4: Add BDD Scenarios (only the ones AC can't cover alone)

Scenarios are optional. Include them when the AC involves a multi-step sequence that's hard to express as a single condition. Here, the threshold routing (AC3) and concurrent session conflict (AC4 edge case) benefit from scenarios.

```gherkin
Scenario: Manager approves a low-value PO suggestion
  Given I am a warehouse manager with a pending suggestion estimated at $3,200
  When I click "Approve" and confirm
  Then the suggestion status changes to "Approved"
  And a PO document is available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue

Scenario: Suggestion already actioned by another session
  Given a suggestion has already been dismissed
  When I attempt to approve it
  Then I see: "This suggestion is no longer available. It may have been approved or dismissed."
  And I am returned to the Pending queue

Scenario: PO value is exactly $5,000
  Given I approve a suggestion with estimated value of exactly $5,000
  When the approval is confirmed
  Then the PO is routed to procurement approval — the $5,000 threshold is inclusive

Scenario: API failure during approval
  Given I confirm the approval action
  When the system returns an error before completing
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion remains in Pending status — no partial state is saved
```

---

### Fix 5: Add project-specific NFRs and Out of Scope — remove the FR section

A Functional Requirements section duplicates AC and introduces shape prescription risk. Every "The system must..." statement that describes observable behavior belongs in AC. Audit, logging, and performance obligations belong in NFRs.

```
Before:
## Functional Requirements
- FR1: The system must display a confirmation summary...  ← already AC1
- FR2: The system must route POs above threshold...       ← already AC3
- FR3: The system must generate a document within 10s...  ← NFR (Performance)
- FR4: The system must log every approval with...         ← NFR (Auditability)
- FR5: The system must prevent duplicate approvals...     ← now AC6

After:
## Non-Functional Requirements
Performance: The approval action must complete within 3 seconds; document generation within 10 seconds.
Security: Only Warehouse Manager and Procurement Lead roles may approve suggestions, restricted to their assigned location.
Reliability: If document generation fails, the approval is still recorded and the document queued — the manager is notified when it becomes available.
Auditability: Every approval action must be logged with actor, timestamp, original suggestion values, and resulting PO status.

## Out of Scope
- Bulk approval of multiple suggestions in a single action
- Modifying the suggested vendor or quantity during the approval flow (see US4)
- Direct API submission to vendor portals (Phase 2)
- Automated approval without manager review
```

---

## Score Comparison

| Dimension | Before | After |
|---|---|---|
| User Clarity (incl. "In order to") | 1/3 | 3/3 |
| Business Value | 1/3 | 3/3 |
| AC Quality | 1/3 | 3/3 |
| BDD Scenarios | 1/3 | 3/3 |
| Story Size | 2/3 | 3/3 |
| Edge Cases | 1/3 | 3/3 |
| Dependency Clarity | 1/3 | 2/3 |
| Conciseness / NFR / Out of Scope | 1/3 | 3/3 |
| **Total** | **9/24** | **23/24** |

**Status:** ❌ Requires Rework → ✅ Dev-Ready

---

## Time Investment vs. Time Saved

| Step | Time |
|---|---|
| Original story (first draft) | 30 seconds |
| Lean template + AI-assisted AC and scenarios | 15 minutes |

**What the 15-minute investment prevents:**

| Avoided cost | Estimated time |
|---|---|
| Grooming clarification session | 30 min |
| Engineering questions during sprint (x3) | 45 min |
| QA cycle to catch missing routing behavior | 2 hrs |
| Follow-up story to add error state | 1 hr |
| **Total avoided** | **~4.5 hours** |

**ROI: ~18x.**

---

## Key Lesson

The lean template is not bureaucracy. Each section prevents a specific category of failure:

| Section | Failure it prevents |
|---|---|
| "In order to" + specific persona | Wrong feature built for the wrong user; privilege bugs |
| Context paragraph | Engineers build without understanding why — wrong tradeoffs |
| Numbered, declarative AC | QA discovers missing behaviors in production, not in review |
| BDD Scenarios (optional) | Complex sequences misunderstood; boundary conditions missed |
| No FR section | Shape prescription avoided; AI doesn't build the structure the story invented |
| Project-specific NFRs | Performance and security treated as afterthoughts |
| Out of Scope | Scope creep disguised as "while we're at it" |

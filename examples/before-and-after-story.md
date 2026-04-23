# Before and After: Story Transformation

This document shows the complete transformation of a poor-quality story into a dev-ready story using the canonical template.

---

## The Original Story (First Draft)

```
As a user, I want to approve purchase order suggestions so that POs get created.
```

**Score:** 9/18 — Requires Rework

**Issues:**
- "user" — no role, no context
- "so that POs get created" — system action, not a user benefit
- No Acceptance Criteria
- No BDD Scenarios
- No Problem Context or Desired Outcome
- Contains no Functional or Non-Functional Requirements
- No Out of Scope defined

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

**Why it matters:** The persona tells engineers who can access this feature (preventing privilege bugs). The "In order to" ties it to the business goal. The "So that" sets the UX success bar — the document must be immediately available, meaning the queue must clear after approval.

---

### Fix 2: Add Problem Context

The original story gave no context. A developer reading it had no idea why this mattered or what they were replacing.

```
Current situation: Managers export a stock report, identify low-inventory items in Excel,
and manually create POs in the vendor portal — one vendor at a time, 8–15 minutes each.

Pain points:
- Each PO takes 8–15 minutes to create manually
- No audit trail exists for who created the PO or when
- Managers on leave leave no fallback process

Operational risks: Stockouts when managers are absent; no coverage model exists.
Constraints: POs ≥ $5,000 require procurement lead approval before submission.
```

---

### Fix 3: Add testable Acceptance Criteria

```
Before: (none)

After:
- AC1: A manager viewing a pending suggestion can approve it via a single confirm action
  after reviewing a summary of vendor, quantity, and estimated value
- AC2: When approved PO value < $5,000, the document is immediately available for download
- AC3: When approved PO value ≥ $5,000, PO transitions to "Pending Procurement Approval"
  and the procurement lead is notified
- AC4: When the approval action fails, the suggestion status does not change and
  the manager sees a recoverable error message
- AC5: An approved suggestion is removed from the Pending queue
```

Each criterion is pass/fail. A QA engineer can test each independently.

---

### Fix 4: Add BDD Scenarios (all four types)

```gherkin
# Happy Path
Scenario: Manager approves a low-value PO suggestion
  Given I am a warehouse manager with a pending suggestion estimated at $3,200
  When I click "Approve" and confirm
  Then the suggestion status changes to "Approved"
  And a PO document is available for download within 10 seconds
  And the suggestion no longer appears in my Pending queue

# Validation
Scenario: Suggestion already actioned by another session
  Given a suggestion has already been dismissed
  When I attempt to approve it
  Then I see: "This suggestion is no longer available. It may have been approved or dismissed."
  And I am returned to the Pending queue

# Edge Case
Scenario: PO value is exactly $5,000
  Given I approve a suggestion with estimated value of exactly $5,000
  When the approval is confirmed
  Then the PO is routed to procurement approval — the $5,000 threshold is inclusive

# Failure Handling
Scenario: API failure during approval
  Given I confirm the approval action
  When the system returns an error before completing
  Then I see: "Approval could not be completed. Please try again."
  And the suggestion remains in Pending status — no partial state is saved
```

---

### Fix 5: Add Functional and Non-Functional Requirements

Functional Requirements express observable system obligations — not implementation:

```
FR1: The system must display a confirmation summary before the manager commits to approval
FR2: The system must route POs at or above the configured threshold to procurement approval
FR3: The system must generate a downloadable document within 10 seconds for below-threshold POs
FR4: The system must log every approval with user ID, timestamp, and original values
FR5: The system must prevent duplicate approvals from double-submit
```

Non-Functional Requirements are measurable:
```
Performance: Approval action completes within 3 seconds; document within 10 seconds
Security: Only Warehouse Manager and Procurement Lead roles may approve suggestions
Reliability: If document generation fails, approval is still recorded; document queued for retry
Auditability: Every state transition is immutably logged with actor and timestamp
```

---

## Score Comparison

| Dimension | Before | After |
|---|---|---|
| User Clarity | 1/3 | 3/3 |
| Business Value | 1/3 | 3/3 |
| AC Quality | 1/3 | 3/3 |
| Story Size | 2/3 | 3/3 |
| Edge Cases | 1/3 | 3/3 |
| Dependencies | 1/3 | 2/3 |
| **Total** | **7/18** | **17/18** |

**Status:** ❌ Requires Rework → ✅ Dev-Ready

---

## Time Investment vs. Time Saved

| Step | Time |
|---|---|
| Original story (first draft) | 30 seconds |
| Full template + AI-assisted AC and scenarios | 15 minutes |

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

The canonical template is not bureaucracy. Each section prevents a specific category of failure:

| Section | Failure it prevents |
|---|---|
| Specific persona | Privilege escalation bugs; wrong feature built for wrong user |
| Problem Context | Engineers build without understanding why — wrong tradeoffs |
| Measurable "So that" | UX bar is unclear — engineers over- or under-deliver |
| BDD Scenarios | QA discovers edge cases in production, not in review |
| Functional Requirements | Behavior is ambiguous — engineers make inconsistent decisions |
| Non-Functional Requirements | Performance and security treated as afterthoughts |
| Out of Scope | Scope creep disguised as "while we're at it" |

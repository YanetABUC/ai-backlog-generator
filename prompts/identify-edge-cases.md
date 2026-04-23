# Prompt: Identify Edge Cases

Use this prompt to surface edge cases that aren't covered in a story's acceptance criteria.

---

## When to Use

- After writing initial acceptance criteria (before evaluation)
- When engineering raises "what about X?" during sprint
- As a pre-sprint quality check
- When a feature has complex business rules or permissions

---

## Core Prompt

```
You are a senior QA engineer and business analyst with experience in finding edge cases that break software.

For the following user story, identify edge cases that must be defined before development starts.

## Edge Case Categories to Cover

**Data edge cases:**
- What if the input is empty or null?
- What if the input is at the maximum allowed length?
- What if the input contains special characters (", ', <, >, /, %)
- What if numbers are 0, negative, or extremely large?
- What if dates are in the past, future, or at a boundary (today, same day as expiry)?

**Permission and role edge cases:**
- What if the user's role changes mid-session?
- What if the user has access to the feature but not to the specific record?
- What if a user tries to perform an action on a resource owned by another user?
- What if the user has partial permissions (can view but not edit)?

**State and timing edge cases:**
- What if the record's state changes between when the user loads the page and submits the form?
- What if two users try to modify the same record simultaneously?
- What if the action is triggered twice quickly (double-submit)?
- What if a multi-step process is interrupted mid-way?

**System and integration edge cases:**
- What if the downstream API is slow (timeout)?
- What if the downstream API returns an unexpected error?
- What if the database write fails after the API call succeeds?
- What if a file upload is interrupted?
- What if a third-party service is unavailable?

**Volume and scale edge cases:**
- What if the list has 0 items? 1 item? 1,000 items?
- What if a text field is empty vs. whitespace-only?
- What if a search returns no results?

**Browser and device edge cases (for UI features):**
- What if the user is on mobile with a slow connection?
- What if the user uses the browser back button mid-flow?
- What if the user has two tabs open with the same session?
- What if the user's session expires mid-flow?

## User Story

[paste the story and its current acceptance criteria]

## Context

**Product:** [brief description]
**Domain:** [what area of the system this touches]
**Known business rules:** [any rules already documented]

## Output

For each edge case:
1. Describe the scenario in one sentence
2. State the expected behavior
3. Write it as a Given/When/Then acceptance criterion
4. Mark severity: Critical (must handle) / High (should handle) / Low (nice to have)

Only include edge cases that are genuinely possible — don't fabricate unrealistic scenarios.
```

---

## Variant: Edge Cases for a Specific Category

When you want to focus on one risk area:

```
Generate edge cases for the following user story, focusing ONLY on [permission edge cases / data edge cases / concurrent access / integration failures].

Story: [paste]
Context: [paste]
```

---

## Variant: Edge Cases From Business Rules

```
Given the following business rules for [feature], generate the edge cases that arise when these rules interact.

Business rules:
1. [rule 1]
2. [rule 2]
3. [rule 3]

Feature: [describe the feature]

Focus on:
- Cases where two rules conflict
- Cases where a rule applies at the boundary (exactly at the threshold)
- Cases where a rule might be circumvented by a technical workaround
```

---

## Example Output

**Story:** Customer can submit a refund request for a delivered order.

**Edge Cases Identified:**

---
**EC-1: Order delivered exactly 30 days ago**
- Scenario: Customer requests refund on the exact 30-day mark
- Expected: Request is allowed (30-day window is inclusive of day 30)
- Given/When/Then: Given my order was delivered exactly 30 days ago, when I click "Request Refund," then I see the refund form and can submit
- Severity: **Critical** — boundary condition likely to cause bugs

---
**EC-2: Customer submits form twice (double-submit)**
- Scenario: Customer clicks Submit twice quickly due to slow API response
- Expected: Only one refund request is created; second submission is blocked or ignored
- Given/When/Then: Given I click Submit on the refund form, when I click Submit again before receiving confirmation, then only one refund request is created and I see a single confirmation
- Severity: **High** — common UX bug

---
**EC-3: Order status changes from "delivered" to "return in progress" mid-session**
- Scenario: Support manually changes order status while customer is filling the form
- Expected: Submission fails gracefully with a clear message
- Given/When/Then: Given I open the refund form for a delivered order, when an admin changes the order status before I submit, then I see: "This order is no longer eligible for a refund request. Please contact support." — the request is not created
- Severity: **High** — state conflict

---
**EC-4: API failure during submission**
- Scenario: Network error occurs when customer clicks Submit
- Expected: Form is preserved, error shown, user can retry
- Given/When/Then: Given I submit the refund form, when the API returns a 500 error, then I see: "Something went wrong. Your request was not submitted." — the form is not cleared and I can retry
- Severity: **Critical** — data loss risk

---
**EC-5: Customer has no delivered orders**
- Scenario: Customer navigates to order history but all orders are pending or cancelled
- Expected: "Request Refund" button is not shown for ineligible orders
- Given/When/Then: Given my order history contains only orders with status "pending" or "cancelled," when I view my order history, then no orders show a "Request Refund" button
- Severity: **High** — permissions/state

---
**EC-6: Refund reason contains SQL injection or script tags**
- Scenario: Customer enters malicious input in the notes field
- Expected: Input is sanitized; attack is blocked
- Given/When/Then: Given I enter `<script>alert(1)</script>` in the notes field, when the form is submitted, then the input is stored as plain text — no script executes and the system does not error
- Severity: **Critical** — security

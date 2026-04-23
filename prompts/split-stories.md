# Prompt: Split Stories

Use this prompt to break a large user story into smaller, sprint-sized stories without losing scope.

---

## When to Use

- Story is estimated as L or XL
- Story spans multiple user interactions or subsystems
- Story has more than 7–8 acceptance criteria
- Engineering says "this is at least 2 weeks of work"
- Story title contains "and" ("User can create and manage...")

---

## The Splitting Principles

Never split by technical layer (frontend / backend / database). That creates stories with no user value. Always split by:

1. **User action** — one story per distinct thing the user does
2. **User type** — one story per role if behavior differs significantly
3. **Workflow step** — one story per step in a multi-step process
4. **Data state** — one story per meaningful state (empty, populated, error)
5. **Frequency** — one story for the common case, one for the edge case
6. **Scope boundary** — MVP behavior in story 1, enhancements in story 2+

---

## Core Prompt

```
You are a senior product manager splitting an oversized user story into smaller, sprint-ready stories.

## Splitting Rules

1. Each resulting story must deliver standalone user value — no "invisible" backend-only stories
2. Split by user action or workflow step, NOT by technical layer
3. If a technical prerequisite is needed (schema change, API creation), create a technical story as a dependency — clearly label it as a technical story, not a user story
4. Every resulting story must be completable in 1–3 days of engineering work
5. Each resulting story must have its own acceptance criteria — do not share criteria across stories
6. Mark the delivery order: which stories are blockers for others?

## Original Story

[paste story here]

## Context

**Epic:** [epic name]
**Constraints:** [time, technical, scope]
**Domain context:** [relevant entities or codebase notes]

## Output Format

For each resulting story:

---
**Story [N]: [Title]**
**As a** [persona], **I want** [action] **so that** [outcome].

**Acceptance Criteria:**
- Given ... when ... then ...

**Size:** S / M
**Type:** User story / Technical story
**Depends on:** Story [N] / None
**Blocks:** Story [N] / None
---

After all stories, provide:
**Delivery order:** [story sequence with brief reasoning]
```

---

## Variant: Split by INVEST Criteria

```
The following story violates one or more INVEST principles. Identify which principles are violated and split the story to fix them.

INVEST:
- Independent — can be developed without waiting for another story
- Negotiable — scope is flexible, not a fixed contract
- Valuable — delivers value to a user (not just to the system)
- Estimable — team can size it
- Small — fits in a sprint
- Testable — has clear pass/fail criteria

Story: [paste]

For each INVEST violation, describe:
1. Which principle is violated
2. How to split or rewrite to fix it
```

---

## Variant: MVP vs. Enhanced Split

```
Split the following story into two parts:
1. MVP version — the minimum to deliver the core user value
2. Enhanced version — the additional features that improve the experience

The MVP version should ship first and be independently valuable.
The Enhanced version is a follow-up story.

For each version, write the full story with acceptance criteria.

Original story: [paste]
```

---

## Example

**Original (too large):**

> As a procurement manager, I want to manage vendor contracts so that I can keep track of renewal dates, contact information, and compliance documents.

**Split result:**

---
**Story 1: Procurement manager adds a new vendor contract**
**As a** procurement manager, **I want** to add a new vendor contract with key details **so that** the contract is tracked in the system before its start date.

**Acceptance Criteria:**
- Given I'm on the vendor contracts page, when I click "Add Contract," then I see a form with: vendor name (required), contract start date (required), contract end date (required), contact name (optional), contact email (optional)
- Given I submit the form with valid required fields, when the contract is saved, then I see it listed in the contracts table sorted by end date ascending
- Given I submit without a required field, when I click Save, then the form shows field-level validation errors — it does not submit

**Size:** S | **Type:** User story | **Depends on:** Story 3 (schema) | **Blocks:** Story 2

---
**Story 2: Procurement manager receives renewal reminder for expiring contracts**
**As a** procurement manager, **I want** to receive an email reminder 30 days before a contract expires **so that** I have time to initiate renewal before the contract lapses.

**Acceptance Criteria:**
- Given a contract exists with an end date 30 days from today, when the daily reminder job runs, then the procurement manager receives an email listing the contract and its end date
- Given a contract has already been flagged as renewed, when the reminder job runs, then no reminder is sent for that contract
- Given no contracts are expiring in 30 days, when the job runs, then no email is sent

**Size:** M | **Type:** User story | **Depends on:** Story 1 | **Blocks:** None

---
**Story 3: Add vendor_contracts table to support contract management**
*(Technical story — no user-facing value, but blocks Stories 1 and 2)*

**Acceptance Criteria:**
- Schema includes: id, vendor_name, start_date, end_date, contact_name, contact_email, status (enum: active/expired/renewed), created_by, created_at, updated_at
- Migration is reversible
- Appropriate indexes on end_date and status

**Size:** S | **Type:** Technical story | **Depends on:** None | **Blocks:** Stories 1 and 2

---
**Delivery order:** Story 3 → Story 1 → Story 2

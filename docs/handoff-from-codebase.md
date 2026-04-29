# Generating Backlog from a Codebase

## Why the Codebase Is a Product Artifact

Most PMs treat the codebase as an engineering concern. That's a missed opportunity.

The codebase reveals:
- What's already built (avoid duplicating work or specifying what exists)
- How the data model works (shape your stories around real entities)
- What patterns exist (new stories should follow existing conventions)
- What's missing (gaps between the code and the product vision)
- What's fragile (areas where new stories need extra care)

This document explains how to extract product-relevant insights from a codebase — with or without deep technical knowledge.

---

## What You Need

You don't need to read every file. You need a structured way to ask questions. This process works best with:
- Claude Code (can read the entire codebase)
- A technical teammate who can run targeted queries
- Access to the repository (even read-only)

---

## Step 1: Map the Domain Model

Before writing stories, understand what entities the system works with.

**Prompt for Claude Code:**
```
Read the codebase and identify the core domain entities — the main data models, database tables, or types that the business logic is built around.

For each entity, tell me:
1. Its name and what it represents in business terms
2. Its key fields (especially those with business meaning)
3. Its relationships to other entities
4. Any states or statuses it can be in
5. Any notable constraints (unique fields, required fields, calculated fields)

Ignore infrastructure, config, and utility files. Focus on business domain.
```

This gives you the product's "vocabulary" — you'll use these entity names and concepts in your stories.

---

## Step 2: Map Existing Capabilities

Understand what the system already does to avoid re-specifying it.

**Prompt for Claude Code:**
```
Based on the codebase (routes, controllers, services, API endpoints), list the capabilities the system currently provides.

Group them by domain area (e.g., User Management, Inventory, Orders, Reporting).

For each capability, describe:
1. What action it performs
2. Who can perform it (if roles/permissions are evident)
3. What data it reads or writes
4. Any notable constraints or business rules embedded in the code

Focus on behavior, not implementation details.
```

Compare this to your product roadmap. Items the system already handles should not be re-specified. Items missing from this list are backlog candidates.

---

## Step 3: Identify Gaps Between Code and Product Vision

This is where backlog items come from.

**Prompt for Claude Code:**
```
I'm going to describe our product vision for [feature area]. Compare this to the codebase and identify:

1. Gaps — things the vision requires that the code doesn't currently support
2. Partial implementations — things that are started but not complete
3. Constraints — places where the existing code will limit or shape how the vision can be implemented
4. Technical debt — areas where the code needs refactoring before the vision can be built cleanly

Product vision:
[paste your vision, brief, or feature description]
```

The gaps become your epics. The constraints become your technical context for stories. The partial implementations become refinement stories.

---

## Step 4: Extract Business Rules From Code

Business rules encoded in the application are often undocumented elsewhere. Surface them before writing stories — otherwise your stories will contradict the existing system.

**Prompt for Claude Code:**
```
Identify business rules embedded in the codebase for [domain area]. These are rules that determine:
- When an action is allowed or blocked
- How a value is calculated
- What states a record can transition between
- What validations are applied to user input
- What notifications or side effects are triggered

Describe each rule in plain English, as if explaining it to a non-technical stakeholder.
```

These rules become acceptance criteria, edge cases, and constraints in your stories.

---

## Step 5: Identify UX Gaps, Broken Processes, and Missing Scenarios

The codebase shows what was built — but not what was left out, what breaks under real conditions, or what the user has no way to recover from. This step surfaces those blind spots before they become production bugs or support escalations.

**Prompt for Claude Code:**
```
Analyze the codebase for [domain area] and identify:

1. UX gaps — user-facing flows that have no error state, no empty state, no loading state, or no way for the user to recover from a failure
2. Broken processes — workflows where a record can reach a state it cannot exit, where a required step has no implementation, or where a multi-step flow has no rollback if a later step fails
3. Missing scenarios — edge cases and boundary conditions that the code does not handle: empty collections, null/missing required data, permission boundary cases, concurrent actions on the same record, actions at volume limits

For each issue found:
- Describe the gap in plain English
- Identify the affected user persona and the point in their workflow where the gap occurs
- Assess the impact: is this a silent failure, a visible error with no recovery, or a data integrity risk?
```

The output maps directly to BDD Failure Handling and Edge Case scenarios — and often reveals stories that are missing from the backlog entirely.

---

## Step 6: Generate Stories From Codebase Analysis

Once you have the gap analysis and business rules, generate stories:

**Prompt:**
```
Based on the following codebase analysis, generate user stories for [feature area].

Domain entities: [from Step 1]
Existing capabilities: [from Step 2]
Identified gaps: [from Step 3]
Business rules: [from Step 4]
UX gaps and missing scenarios: [from Step 5]
Product vision: [your brief]

For each story:
- Lead with "In order to [business goal]", then "As a [specific persona]", "I want to [action]", "So that [measurable user benefit]"
- Write Acceptance Criteria as declarative pass/fail statements — not Given/When/Then
- Write BDD Scenarios in Given/When/Then format with all four types: Happy Path, Validation, Edge Case, Failure Handling
- Note any dependencies on existing capabilities
- Flag any technical constraints surfaced in the analysis

Focus on the gaps. Do not re-specify what already exists.
```

---

## Example: Adding a Feature to an Existing E-Commerce Platform

**What the codebase revealed:**
- `Order` entity has status: `pending`, `paid`, `shipped`, `delivered`, `cancelled`
- No `refund_requested` or `refund_approved` status exists
- `Payment` entity has a `gateway_transaction_id` but no refund tracking fields
- An existing `email_notification` service handles order status changes

**What the product vision specified:**
- Allow customers to request refunds within 30 days of delivery
- Require manager approval for refunds over $100
- Auto-approve refunds under $100
- Send confirmation email on approval/rejection

**Stories generated from gap analysis:**
1. Customer can submit a refund request for a delivered order (within 30-day window)
2. System auto-approves refund requests under $100
3. Manager receives notification for refund requests over $100
4. Manager can approve or reject pending refund requests
5. Customer receives email confirmation when refund is approved or rejected
6. Order status transitions to `refund_requested` and `refund_approved` appropriately

**What was avoided:** Re-specifying email notification (already exists), re-specifying order status structure (already understood), re-specifying payment gateway integration (already present)

---

## Workflow Reference

Full step-by-step process: [Workflow 03 — Codebase to Backlog](../workflows/03-codebase-to-backlog.md)

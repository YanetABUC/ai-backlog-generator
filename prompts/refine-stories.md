# Prompt: Refine Stories

Use this prompt to improve an existing user story that has been flagged during evaluation. Output must conform to the canonical story template.

---

## When to Use

- After story evaluation identifies quality gaps
- When a story was written manually and needs structure
- When a story is too vague, too large, or missing key sections
- When a story contains prohibited sections (Implementation Notes, Technical Design, Pseudocode)

---

## Core Prompt

```
You are a senior business analyst refining a user story to make it dev-ready.

The story below has quality issues. Rewrite it to fix those issues while preserving the core intent.

## Issues to Fix

[List specific issues from evaluation, e.g.:]
- Persona is too generic ("user" with no context)
- "So that" clause is circular — states a system action, not a user benefit
- Acceptance criteria are not testable
- BDD scenarios are missing
- Functional Requirements are prescriptive (describe how, not what)
- Story contains Implementation Notes — prohibited section

## Refinement Rules

1. Preserve the core intent — do not add scope that wasn't implied
2. Persona must include role + context (never just "user")
3. "So that" must state a measurable user benefit — not "so that it works"
4. Every AC must be pass/fail testable and deterministic
5. BDD Scenarios must cover: Happy Path, Validation, Edge Case, Failure Handling
6. Functional Requirements describe WHAT the system does — never HOW
7. Non-Functional Requirements must be measurable where possible
8. Remove any section that prescribes implementation:
   - Implementation Notes, Technical Design, Pseudocode, file paths, API specs, code blocks

## Story Format

Rewrite using this exact structure:

---
# US{N} — [Short Title]

## User Story
**In order to** [business goal],
**As a** [specific persona],
**I want to** [action],
**So that** [measurable benefit].

## Business Objective
[One sentence — measurable business goal or OKR connection]

## Problem Context
**Current situation:** [how it works today]
**Pain points:** [bullet list]
**Operational risks:** [what goes wrong without this]
**Constraints:** [limits]

## Desired Outcome
**User impact:** [workflow change]
**Business impact:** [measurable result]
**Success metrics:** [metric | baseline | target]

## Acceptance Criteria
- AC1: [observable, pass/fail]
- AC2: [observable, pass/fail]
- AC3: [error state]
- AC4: [edge case]

## Scenarios (BDD)
[Happy Path / Validation / Edge Case / Failure Handling — all four required]

## Functional Requirements
- FR1: The system must [observable behavior]
- FR2: The system must [observable behavior]
- FR3: The system must [validation or error behavior]

## Non-Functional Requirements
[Performance / Security / Reliability / Auditability / Scalability]

## Out of Scope
[explicit exclusions]

## Media
[Design / Prototype / Research links]
---

## Original Story

[paste the story here]

## Evaluation Feedback

[paste specific issues from the evaluation]

## Product Context

[paste any relevant domain context, user types, or business rules]
```

---

## Variant: Fix a Single Section

When only one section needs repair, target it:

```
Rewrite ONLY the [section name] section of the following user story.

The current section has this problem: [describe issue]

Requirements for the rewritten section:
[specific requirements for that section]

Keep all other sections exactly as written.

Full story: [paste]
```

---

## Variant: Remove Prohibited Sections

When a story contains Implementation Notes, Technical Design, or Pseudocode:

```
The following user story contains prohibited sections that prescribe implementation.

Remove the following sections entirely:
[list sections to remove]

Convert any product-relevant behavior from those sections into:
- Acceptance Criteria (if it describes observable behavior)
- Functional Requirements (if it describes a system obligation)
- Non-Functional Requirements (if it describes performance, security, or reliability)
- Out of Scope (if it explicitly excludes something)

Discard anything that is purely implementation detail — it belongs in the technical spec, not the story.

Story: [paste]
```

---

## Variant: Split an Oversized Story

```
The following story is too large for a single sprint. Split it into 2–4 smaller stories.

Splitting rules:
- Split by user action or workflow step — not by technical layer
- Each resulting story must deliver standalone user value
- If a technical prerequisite is needed (e.g., schema addition), create a separate technical story — clearly labeled
- Every resulting story must have all template sections filled
- Mark delivery order: which stories block which

For each split story, use the full canonical template format.

Original story: [paste]
Context: [paste]
```

---

## Before and After: Quick Reference

| Before (common issues) | After (correct) |
|---|---|
| "As a user, I want..." | "As a warehouse manager reviewing my daily queue, I want to..." |
| "So that POs get created" | "So that a PO document is available for download within seconds" |
| "The system should work correctly" | Given/When/Then with exact expected behavior |
| "Files to modify: order_service.py" | Removed — belongs in technical spec |
| "Performance: fast" | "Performance: approval action completes within 3 seconds under normal load" |
| Happy path only in scenarios | Happy Path + Validation + Edge Case + Failure Handling |

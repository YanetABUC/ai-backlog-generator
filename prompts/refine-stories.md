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
2. Story must start with "In order to" — a specific business goal, not a feature description
3. Persona must include role + context (never just "user")
4. "So that" must state a measurable user benefit — not "so that it works"
5. Collapse separate Business Objective / Problem Context / Desired Outcome sections into a single Context paragraph (2–5 sentences)
6. Every AC must be a numbered, pass/fail declarative statement — no Given/When/Then in AC
7. BDD Scenarios are optional — include only when AC cannot express a complex multi-step interaction (max 5)
8. Remove any Functional Requirements (FR1, FR2...) section — move observable behaviors into AC; move audit/logging requirements into NFRs
9. Remove any shape prescription: return type enumerations, result object structures, outcome variants, function signatures — rewrite as observable behavior in AC
10. NFRs must be project-specific with measurable thresholds — remove boilerplate
11. Remove any section that prescribes implementation: Implementation Notes, Technical Design, Pseudocode, file paths, API specs, code blocks
12. Target 80–150 lines — if the story exceeds 200 lines after refinement, flag it for further reduction

## Story Format

Rewrite using this exact structure. Target 80–150 lines.

---
# US{N} — [Short Title]

## User Story
**In order to** [business goal],
**As a** [specific persona],
**I want to** [action],
**So that** [measurable benefit].

> **Ownership boundary:** This story defines *what behavior* the system must exhibit. Implementation decisions — data structures, return types, function signatures, error handling patterns — are owned by the development team.

## Context
[2–5 sentences: current situation, pain point or gap, what changes when this ships. One paragraph only.]

## Acceptance Criteria
1. [observable, pass/fail]
2. [observable, pass/fail]
3. [error state]
4. [edge case]

## Scenarios (BDD)
*Only when AC cannot express a complex multi-step interaction. Omit if AC is sufficient. Max 5.*
[Happy Path / Validation / Edge Case / Failure Handling — only as needed]

## Non-Functional Requirements
*Story-specific only. Omit if none apply.*
[Performance / Security / Reliability / Auditability / Scalability — with specific thresholds]

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

When a story contains Implementation Notes, Technical Design, Pseudocode, Functional Requirements, or redundant narrative sections:

```
The following user story contains prohibited sections that prescribe implementation or add redundancy.

Remove the following sections entirely:
[list sections to remove]

Convert any product-relevant behavior from those sections into:
- Acceptance Criteria (if it describes observable behavior)
- Non-Functional Requirements (if it describes performance, security, reliability, or auditability)
- Out of Scope (if it explicitly excludes something)

Do NOT create a Functional Requirements section — all observable behaviors belong in AC.
Do NOT preserve shape prescription (return types, outcome enumerations, result structures) — rewrite as behavior.
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
| "As a user, I want..." | "In order to [goal], As a warehouse manager reviewing my daily queue, I want to..." |
| "So that POs get created" | "So that a PO document is available for download within seconds" |
| Separate Business Objective + Problem Context + Desired Outcome sections | Single Context paragraph (2–5 sentences) |
| Functional Requirements (FR1, FR2...) section | Removed — behaviors moved into numbered AC items |
| "Return one of: Pre-answer / Block / Resolved" | Removed — shape prescription; rewrite as: "When the color is resolved, it is pre-answered; otherwise it is skipped" |
| "The system should work correctly" | Declarative AC item: "When X occurs, the system does Y" |
| "Files to modify: order_service.py" | Removed — belongs in technical spec |
| "Performance: fast" | "Performance: approval action completes within 3 seconds under normal load" |
| All four BDD Scenarios always required | Scenarios omitted when AC is sufficient |

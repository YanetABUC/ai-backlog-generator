# Workflow 03: Codebase to Backlog

**Time:** 2–4 hours  
**Input:** Existing codebase + product vision or feature request  
**Output:** Gap-based backlog grounded in technical reality

---

## Overview

When you're adding features to an existing system, the codebase is your most reliable source of product truth. It tells you what exists, what constraints apply, and what risks to watch out for. This workflow uses AI to analyze the codebase and generate a backlog grounded in what's actually there.

**Best used when:**
- Adding features to a mature product
- Taking over a product without complete documentation
- Estimating scope for a new initiative
- Reducing "we didn't know that existed" surprises in sprint

**Cost principle:** The analysis artifacts produced in Steps 2–6 (domain model, capability map, gap analysis, business rules, risk areas) should be produced **once per feature area** and reused across all story generation sessions for that area. Regenerating them per session multiplies token cost without improving quality. Save the outputs and reference them in subsequent sessions.

---

## Prerequisites

- [ ] Access to the codebase (read access is sufficient)
- [ ] Claude Code, or a technical teammate who can run targeted analysis
- [ ] A product vision, feature request, or initiative brief

---

## Step 1: Define the Target Feature Area

Before analyzing the codebase, be specific about what you're building. A focused analysis beats a broad one.

Write a one-paragraph feature intent:
```
We want to [what], for [who], because [why].
This will live in [part of the product].
It will interact with [known entities or flows].
It should not affect [out-of-scope areas].
```

Example:
```
We want to add a refund management workflow, for operations managers, because our current process is handled entirely via email and takes 3+ days. This will live in the Orders section of the admin panel. It will interact with Order records, Payment records, and the email notification system. It should not affect the customer-facing checkout or payment flow.
```

---

## Step 2: Map the Domain Model

**Claude Code prompt:**
```
Read the codebase and identify all domain entities relevant to [feature area].

For each entity:
1. Name and business definition
2. Key fields with business meaning (skip technical/infrastructure fields)
3. Status or state machine (if applicable)
4. Relationships to other entities
5. Existing business rules embedded in the code (validations, constraints, calculations)

Focus on: [list relevant directories or files if known]
Feature area: [paste your Step 1 intent]
```

Output: A domain map you'll reference in every subsequent step.

---

## Step 3: Map Existing Capabilities

**Claude Code prompt:**
```
Based on the codebase, list every capability the system currently provides related to [feature area].

Format each capability as:
- Action: what the system does
- Trigger: what initiates it (user action, API call, scheduled job, event)
- Actor: who can perform it (role/permission if evident)
- Input: what data it consumes
- Output: what it produces or modifies
- Business rules: any logic embedded in the implementation

Group by functional area.
Feature area: [paste Step 1 intent]
```

Output: A "what we already have" inventory. Cross-check against your feature vision to avoid re-specifying existing behavior.

---

## Step 4: Identify Gaps

**Prompt (can be Claude Code or regular Claude):**
```
I'm building [feature name]. Based on the following:

Existing capabilities: [paste Step 3 output]
Domain model: [paste Step 2 output]

My product vision:
[paste Step 1 intent + any additional detail]

Identify:
1. Gaps — capabilities my vision requires that don't exist yet
2. Partial implementations — things that are started but incomplete
3. Data gaps — fields or entities that need to be added
4. Integration gaps — external services or APIs that need to be connected

For each gap, indicate whether it's:
- New functionality (build from scratch)
- Extension of existing functionality (modify what's there)
- Data migration or schema change (backend work before features)
- Configuration or environment change (DevOps/infrastructure)
```

These gaps become your epics and stories.

---

## Step 5: Extract Embedded Business Rules

**Claude Code prompt:**
```
For [feature area], extract all business rules currently embedded in the codebase.

Business rules include:
- Validation rules (what input is rejected and why)
- State transition rules (what conditions allow a record to change state)
- Authorization rules (what roles can do what)
- Calculation rules (how values are derived)
- Trigger rules (what events cause side effects)
- Time-based rules (anything that depends on dates, expiry, or scheduling)

Express each rule in plain English suitable for a non-technical stakeholder.
```

These rules become acceptance criteria. They also reveal constraints your stories must respect.

---

## Step 6: Identify Technical Risk Areas

**Claude Code prompt:**
```
Based on the codebase, identify areas of technical risk that could affect the delivery of [feature name].

Look for:
1. High complexity or high coupling in relevant code areas
2. Missing test coverage in areas the feature will touch
3. Deprecated dependencies or patterns the feature would extend
4. Performance concerns (large datasets, N+1 queries, unindexed fields)
5. Security considerations (auth, data exposure, input validation gaps)
6. Areas with known tech debt (commented TODOs, workarounds, legacy patterns)

For each risk, indicate:
- Risk description
- Affected area in the codebase
- Recommended mitigation (spike, refactor story, architectural note in the epic)
```

These risks become technical notes in your epics and may generate separate spike or tech-debt stories.

---

## Step 7: Generate Epics

**Prompt:**
```
Based on the following analysis, generate epics for [feature name]. One epic per major gap or capability cluster.

Domain model: [Step 2]
Existing capabilities: [Step 3]
Identified gaps: [Step 4]
Business rules: [Step 5]
Technical risks: [Step 6]

For each epic, use the 10-section canonical format: Narrative, Strategic Context, Desired Transformation, Scope Definition, Target Users and Actors, High-Level Functional Expectations, Non-Functional Expectations, Constraints and Assumptions, Dependencies, Success Metrics.

Flag all unverified assumptions in Section 8 as [To validate].
Do not generate user stories yet.
```

---

## Step 7b: Resolve [To Validate] Assumptions

**Do not generate stories until this step is complete.**

Review each generated epic for items marked `[To validate]` in Section 8 (Constraints and Assumptions). These are codebase-identified constraints and assumptions that need confirmation before stories can be written accurately.

For each `[To validate]` item:
1. Confirm with engineering, the product owner, or the codebase (ask Claude Code)
2. Update the epic with the resolved value
3. If an assumption cannot be resolved yet, create a Spike story to investigate it before story generation

Only proceed to Step 7c once every epic has no remaining `[To validate]` items.

---

## Step 7c: Generate Stories

**Prompt:**
```
Based on the following validated epics and gap analysis, generate user stories for [feature name].

For each epic, generate 5–8 stories covering: primary happy path, alternative flows, error states, permission variations, edge cases.

Also generate:
- Technical stories for infrastructure, data migration, or refactoring work
- Spike stories for areas of high uncertainty identified in Step 6

For each user story:
- Story format (In order to / As a / I want to / So that — lean canonical template)
- Numbered acceptance criteria (pass/fail, no Given/When/Then)
- Dependencies on existing capabilities or other stories
- Technical context for the engineering team (constraints from the codebase, in the Context paragraph)

Do not re-specify capabilities that already exist. Reference them as "existing behavior."

Validated epics: [paste Step 7 output with resolved assumptions]
Domain model: [Step 2]
Business rules: [Step 5]
```

---

## Step 8: Engineering Validation

Before finalizing the backlog, have a senior engineer review:

- [ ] The gap analysis is accurate
- [ ] Business rules match their understanding of the system
- [ ] Technical risks are correctly identified
- [ ] Story dependencies are in the right order
- [ ] No "invisible" complexity was missed

This review typically catches 1–2 significant issues that save a sprint.

---

## Step 9: Finalize and Import

- [ ] Stories refined based on engineering input
- [ ] Stories evaluated against [quality criteria](../docs/backlog-quality-criteria.md)
- [ ] Stories ordered with blockers first
- [ ] Stories imported to backlog tool with domain map attached as context
- [ ] Codebase analysis artifacts saved and linked from the epic — these are the reusable context for all future story sessions in this feature area; do not regenerate them per session

---

## Output Example Summary

For the refund management example:
- **3 epics:** Refund Request Flow, Approval Workflow, Notification System
- **14 user stories** covering customer submission, manager review, auto-approval, and email notifications
- **2 technical stories:** Add refund_status to Order model, Add refund_amount to Payment model
- **1 spike:** Investigate payment gateway refund API capabilities and limits
- **5 business rules** documented as constraints in acceptance criteria

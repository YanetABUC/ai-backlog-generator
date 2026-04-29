# Workflow 05: Dev-Ready Handoff

**Time:** 30–60 minutes per epic  
**Input:** Evaluated and refined user stories  
**Output:** Zero-ambiguity stories ready for sprint assignment

---

## Overview

Dev-ready doesn't mean "the story is written." It means an engineer can pick up the story, understand everything they need, and start building without scheduling a meeting. That's a higher bar than most teams hold.

This workflow is the final pass before stories enter a sprint.

---

## The Dev-Ready Standard

A story is dev-ready when an engineer who has never spoken to you about it can answer all of these questions from the story alone:

1. Who is this for and why does it matter?
2. What exactly should be built?
3. What does done look like? (Can they test it themselves?)
4. What can go wrong, and how should it be handled?
5. What do they need from other teams before starting?
6. What is out of scope? (So they don't over-build)

---

## Step 1: Pre-Handoff Checklist

Run this before the engineering team sees any story:

```
Story basics:
[ ] Title is an action statement, not a noun ("User can export report" not "Export report")
[ ] User persona is specific (role + context, not "user")
[ ] Business value is explicitly stated
[ ] Story is sized S or M (L stories need splitting)

Acceptance criteria:
[ ] All criteria are declarative pass/fail statements — no Given/When/Then in AC (that belongs in Scenarios)
[ ] Every criterion has a clear pass/fail state and is independently evaluable
[ ] Happy path is covered (at least 2–3 criteria)
[ ] Error states are covered (API failure, invalid input, permission denied)
[ ] Empty state is addressed if the feature displays data
[ ] Edge cases identified from evaluation are included
[ ] No shape prescription — no return type enumerations, outcome variants, or result structures

Technical context:
[ ] Domain entities referenced by business name (from codebase analysis) — not file paths or class names
[ ] Performance constraints stated in NFRs with specific thresholds
[ ] Security or permission requirements explicit in NFRs

Dependencies:
[ ] All blockers identified and linked
[ ] Design assets linked (Figma, prototype URL)
[ ] API documentation linked (if third-party integration)
[ ] All dependencies are resolved or have a resolution date

Definition of Done:
[ ] Unit tests expected (yes/no, and what coverage)
[ ] Integration tests expected (yes/no)
[ ] QA sign-off required (yes/no)
[ ] Documentation update required (yes/no)
[ ] Analytics event to be fired (yes/no, event name)
[ ] Feature flag required (yes/no)
```

---

## Step 2: AI-Assisted Gap Detection

Run this prompt on each story before handoff:

```
You are a senior engineer reviewing a story before accepting it into a sprint.

Read this story and identify:
1. Any information a developer would need that is NOT in the story
2. Any acceptance criteria that are ambiguous or untestable
3. Any edge cases that seem missing given the feature context
4. Any technical assumptions that should be validated before starting
5. Any dependencies not called out in the story

Be specific. "The error state is undefined" is useful. "Needs more detail" is not.

Story:
[paste story]

Product/system context:
[paste relevant domain or codebase context]
```

Address every item before declaring the story ready.

---

## Step 3: Write the Technical Context Block

Every story should include a Technical Context section for engineering. This is written by the PM/BA but uses insights from the codebase analysis or engineering review.

**Template:**
```markdown
## Technical Context

**Relevant entities:** [domain entities involved]
**Existing behavior to preserve:** [what must not change]
**Known constraints:** [technical limitations from codebase analysis]
**Suggested approach (optional):** [only if engineering has pre-validated]
**Performance considerations:** [if applicable]
**Security notes:** [if applicable]
```

Example:
```markdown
## Technical Context

**Relevant entities:** Order (status: pending/paid/shipped/delivered/cancelled), Payment (gateway_transaction_id)
**Existing behavior to preserve:** Order status transitions must go through the existing OrderStateMachine service — do not add direct status updates
**Known constraints:** Payment gateway supports refunds only within 90 days of charge; enforce at API layer
**Performance considerations:** Refund list is loaded per order — no pagination needed for typical order size
**Security notes:** Refund approval must require manager role; customer cannot approve their own refund
```

---

## Step 4: Eliminate Open Questions

Before handoff, every open question must be resolved or explicitly deferred.

**Prompt for resolution:**
```
The following story has these open questions before development can start:
[list open questions]

For each question:
1. Who can answer it?
2. What's the fastest way to get an answer?
3. What's the default behavior if we can't get an answer in time?

Story: [paste story]
```

For questions that can't be resolved before the sprint, document the default assumption the engineer should use. Don't leave questions open and hope engineers will ask.

---

## Step 5: Engineering Pre-Alignment

Before sprint planning, send stories to the engineering lead for a 24-hour async review. Ask them to flag:

- Stories they can't estimate without more information
- Stories with hidden complexity (silent blockers)
- Stories that should be split further
- Stories that have dependency ordering issues

Use this template:
```
Hi [name], attaching the stories for [epic name] ahead of sprint planning.

Please flag before [date]:
- Any story you can't estimate from the information provided
- Any stories with risks or complexity not reflected in the story
- Any ordering issues in the dependency chain

I'll address all flags before we plan. Thanks!
```

---

## Step 6: Final Import and Linking

When importing stories to Jira / Linear / GitHub Issues / Shortcut:

- [ ] Stories are ordered with dependencies first
- [ ] Blockers are linked (not just mentioned in text)
- [ ] Design link is attached (not just in the description)
- [ ] Epic link is set
- [ ] Labels applied (feature, bug, tech-debt, spike)
- [ ] Assignee or team set
- [ ] Sprint assigned

---

## Step 7: Kickoff Note

Instead of a story-by-story walkthrough, send a brief written kickoff note when stories drop:

**Template:**
```
**[Epic Name] — Sprint Kickoff**

What we're building: [1–2 sentences]
Why it matters: [1 sentence — the business goal]
Key constraints to know: [bullet list of 2–3 constraints from codebase/design]
Dependency order: [brief sequence]
Contacts: Design ([name]), PM ([name])

[Link to epic in tracker]

Questions: drop them in [Slack channel] or tag me on the story.
```

This replaces the 30-minute kickoff meeting for well-written stories.

---

## Red Flags That Mean "Not Ready"

| Signal | Action |
|---|---|
| Engineer asks "what should happen when X?" | X is a missing edge case — add it |
| "We need to sync before I start" | Clarify the story before sprint starts |
| "This is bigger than I thought" | Story wasn't properly sized — split it |
| "This conflicts with how Y works" | Codebase analysis was missed — do Step 3 of Workflow 03 |
| "Design hasn't finalized this" | Design dependency wasn't flagged — block the story |

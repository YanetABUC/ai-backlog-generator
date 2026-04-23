# Fast Feedback Loop

## The Principle

The most expensive time to discover a wrong assumption is after you've written 20 stories. The cheapest time is before you start.

The Fast Feedback Loop is a structured process for validating direction in under 2 hours — before any stories are written.

---

## When to Use This

Use the Fast Feedback Loop when:
- You're starting a new feature or initiative
- You're unsure whether the solution direction is right
- Stakeholders have conflicting opinions on scope
- The technical feasibility is unknown
- You're working from a brief or prototype with no user validation

Skip it when:
- You're refining existing, well-understood features
- The scope is clearly defined and uncontested
- You're in bug-fix or maintenance mode

---

## The Loop in Five Steps

### Step 1: Capture Raw Input (15 min)

Collect everything you have:
- Meeting notes or transcripts
- Product brief or one-pager
- Stakeholder emails or Slack threads
- Wireframes or prototype links
- Support tickets or user feedback
- Analytics data or usage patterns

Paste all of it into a single document. Don't organize it yet.

---

### Step 2: Extract Assumptions (30 min)

Use AI to extract the assumptions embedded in your raw input.

**Prompt:**
```
You are a senior business analyst. I'm going to give you raw product input (notes, briefs, ideas).

Your job is to extract all assumptions — stated and unstated — embedded in this content.

Categorize them as:
- User assumptions (who we think the user is and what they need)
- Business assumptions (what we think the outcome will be)
- Technical assumptions (what we think is feasible or already exists)
- Scope assumptions (what we think is in vs. out)

For each assumption, flag whether it has been validated (yes/no/unknown).

Raw input:
[paste your content here]
```

Review the output. Add any assumptions the AI missed. This list is now your validation target.

---

### Step 3: Score Assumption Risk (15 min)

For each assumption, score:
- **Confidence (1–5):** How sure are you this assumption is correct?
- **Impact (1–5):** How wrong would things go if this assumption is false?

**Risk score = (5 - Confidence) × Impact**

Focus validation effort on assumptions with risk score > 12.

| Assumption | Confidence | Impact | Risk Score | Status |
|---|---|---|---|---|
| Users want daily alerts | 2 | 5 | 15 | Validate first |
| API supports bulk export | 4 | 4 | 4 | Acceptable |
| Feature replaces current flow | 1 | 5 | 20 | Critical to validate |

---

### Step 4: Run Targeted Validation (30–60 min)

For each high-risk assumption, choose the fastest validation method:

**User assumption → ask a real user (or proxy)**
- 15-min call with a customer
- Check support ticket patterns
- Review session recordings
- Ask a customer-facing teammate

**Business assumption → check with a stakeholder**
- Async Slack question
- Review OKRs / success metrics
- Check analytics for baseline data

**Technical assumption → check with an engineer**
- 15-min architecture conversation
- Review existing API docs or schema
- Run a codebase analysis (see [Workflow 03](../workflows/03-codebase-to-backlog.md))

**Scope assumption → align in a structured meeting or async doc**
- Write the assumption explicitly
- Ask stakeholders to confirm or reject
- Document the decision

---

### Step 5: Update Direction Before Writing Stories (15 min)

After validation, use AI to update your product direction document:

**Prompt:**
```
Based on the following validated and invalidated assumptions, update the product direction for [feature name].

Original direction: [paste brief]

Validated assumptions: [list]
Invalidated assumptions: [list with what we learned instead]
Open questions: [list]

Output:
1. Updated problem statement
2. Revised solution direction
3. Out-of-scope items (based on invalidated assumptions)
4. Remaining risks to monitor
```

This document becomes the context for all story generation in the next phase.

---

## Fast Feedback Loop Template

```markdown
# Fast Feedback Loop: [Feature Name]
Date: [date]
PM: [name]

## Raw Input Summary
[2–3 sentences summarizing what we started with]

## High-Risk Assumptions
| # | Assumption | Risk Score | Validation Method | Result |
|---|---|---|---|---|
| 1 | | | | |

## Validated Direction
Problem statement: 
Solution direction: 
Out of scope: 
Open risks: 

## Ready to Generate Stories?
[ ] All high-risk assumptions validated or accepted
[ ] Stakeholder alignment confirmed
[ ] Technical feasibility confirmed
[ ] Direction document updated
```

---

## Why This Saves Time

A typical discovery-to-sprint cycle without feedback loops:
- Discovery → 2 weeks
- Story writing → 3 days
- Story refinement → 2 days (because stories were wrong)
- Engineering questions during sprint → +1 day of meetings

A cycle with the Fast Feedback Loop:
- Fast Feedback Loop → 2 hours
- Story writing → 1 day (with AI, context is rich)
- Story refinement → 30 minutes (AI evaluation catches gaps)
- Engineering questions during sprint → minimal

The feedback loop doesn't add time — it relocates it to the cheapest point in the process.
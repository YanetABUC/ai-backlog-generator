# Workflow 01: Discovery to Backlog

**Time:** 2–4 hours  
**Input:** Meeting notes, product brief, stakeholder interviews, problem statement  
**Output:** Validated epics and dev-ready user stories

---

## Overview

This workflow takes raw discovery artifacts and converts them into a structured backlog using AI at each stage. It's designed for teams starting a new initiative from scratch.

---

## Prerequisites

Before starting:
- [ ] At least one of: meeting notes, product brief, user research summary, or problem statement
- [ ] Access to Claude, ChatGPT, or similar LLM
- [ ] 30–60 minutes of uninterrupted time

---

## Step 1: Consolidate Raw Input

Gather all discovery artifacts into one document. Include:
- Meeting notes (verbatim is fine)
- Stakeholder quotes
- User pain points mentioned
- Any numbers, metrics, or constraints mentioned
- Competing products or comparisons made
- Out-of-scope items explicitly mentioned

**Don't clean it up yet.** Messy input is fine — AI handles it.

---

## Step 2: Extract a Structured Problem Statement

**Prompt:**
```
You are a senior product manager. I'm going to give you raw discovery notes from a product planning session.

Extract the following:
1. Problem statement — what problem are we solving and for whom?
2. Target users — who specifically experiences this problem?
3. Current state — how do they solve this problem today, and why is that insufficient?
4. Desired outcome — what does success look like from the user's perspective?
5. Business goal — what does the company gain if this is solved?
6. Constraints — what limitations were mentioned (time, budget, technical, regulatory)?
7. Out of scope — what was explicitly excluded?
8. Open questions — what remains unresolved that could affect the solution?

Raw discovery notes:
[paste your notes]
```

**Review the output.** Correct anything that's wrong. Add anything that's missing. This document is your source of truth for everything that follows.

---

## Step 3: Run the Fast Feedback Loop

Before generating epics, validate your top 3–5 assumptions.

See [fast-feedback-loop.md](../docs/fast-feedback-loop.md) for the full process.

At minimum, answer:
- Are we solving the right problem?
- Is the target user correctly identified?
- Are there technical constraints we haven't surfaced yet?

---

## Step 4: Generate Epics

**Prompt:**
```
Based on the following structured problem statement, generate a set of epics that together represent the full product scope.

Each epic should:
- Represent a meaningful, shippable product capability (not a technical layer)
- Have a clear user benefit
- Be sized so that a team could complete it in 2–6 weeks
- Have a measurable success criterion

For each epic include:
- Epic title (verb + outcome, not feature name)
- User segment
- Business goal
- Success metric
- 4–6 example stories that would fall under this epic (titles only)
- Dependencies on other epics (if any)

Problem statement:
[paste Step 2 output]
```

See full prompt with more options: [generate-epics.md](../prompts/generate-epics.md)

---

## Step 5: Prioritize Epics

Before generating stories, prioritize your epics. AI can help:

**Prompt:**
```
Given the following epics, rank them by delivery priority using the RICE framework:
- Reach: How many users are affected?
- Impact: How significantly does this improve their experience?
- Confidence: How sure are we this will have the stated impact?
- Effort: Relative effort to build (S/M/L/XL)

RICE Score = (Reach × Impact × Confidence) / Effort

Epics:
[paste epic list]

Assumptions about our user base and team capacity:
[add any context you have]
```

Use the RICE ranking to decide which epics to generate stories for first.

---

## Step 6: Generate User Stories

For each prioritized epic, generate stories:

**Prompt:**
```
Generate user stories for the following epic.

Epic: [epic title and description]
User segment: [who this is for]
Business goal: [what we're trying to achieve]
Success metric: [how we measure success]
Constraints: [technical, time, or scope constraints]
Out of scope: [what is explicitly excluded]

For each story:
1. Write in "As a [specific user], I want [action], so that [outcome]" format
2. Include 3–5 acceptance criteria in Given/When/Then format
3. Note the story's size (S/M/L)
4. Flag any dependencies or risks

Generate 5–8 stories that together cover the epic completely, including edge cases and error states.
```

See full prompt: [generate-user-stories.md](../prompts/generate-user-stories.md)

---

## Step 7: Evaluate Story Quality

Run each batch of stories through the quality evaluation process.

**Quick evaluation prompt:**
```
Evaluate the following user stories against this quality rubric. For each story, score it 1–3 on:
1. Clarity of user and context (1=vague, 3=specific)
2. Business value is explicit (1=implied, 3=measurable)
3. Acceptance criteria are testable (1=vague, 3=all pass/fail)
4. Appropriate size (1=too large, 3=sprint-ready)
5. Edge cases covered (1=happy path only, 3=error + edge cases)
6. No ambiguous dependencies (1=unclear, 3=all identified)

For any story scoring below 2 on any dimension, provide a specific fix.

Stories:
[paste stories]
```

See full workflow: [04-story-evaluation.md](../workflows/04-story-evaluation.md)

---

## Step 8: Refine and Finalize

For stories that need improvement, use the refinement prompt:

See [refine-stories.md](../prompts/refine-stories.md)

---

## Step 9: Prepare for Handoff

Before moving stories to your backlog tool:

- [ ] All stories pass quality evaluation (score 12+/18)
- [ ] Stories are ordered by dependency (blockers first)
- [ ] Each story has an owner or assignee
- [ ] Definition of Done is documented on each story
- [ ] Engineers have been given a heads-up (no cold drops)

See [05-dev-ready-handoff.md](../workflows/05-dev-ready-handoff.md)

---

## Estimated Time Per Step

| Step | Time |
|---|---|
| Consolidate raw input | 15 min |
| Extract problem statement | 20 min |
| Fast feedback loop | 60–90 min |
| Generate epics | 20 min |
| Prioritize epics | 15 min |
| Generate stories (per epic) | 20 min |
| Evaluate stories | 20 min |
| Refine stories | 20 min |
| Handoff prep | 15 min |
| **Total** | **~3.5 hours** |

---

## Output Checklist

```
[ ] Structured problem statement documented
[ ] Validated assumptions documented
[ ] Epics generated and prioritized
[ ] User stories generated for top epics
[ ] All stories evaluated and scored 12+/18
[ ] Stories imported into backlog tool
[ ] Engineering team briefed
```

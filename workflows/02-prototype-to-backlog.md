# Workflow 02: Prototype to Backlog

**Time:** 1–3 hours  
**Input:** Figma prototype, wireframes, clickable mockup, or screen recordings  
**Output:** Saved epics and user stories with screen states, backend requirements, and design context embedded

**Claude Code:** `/backlog:prototype-to-backlog`

---

## Overview

Prototypes are underused as backlog inputs. They contain implicit requirements that designers understand but rarely document — interaction states, empty states, error states, loading states, transitions. This workflow systematically extracts all of that into dev-ready stories.

Unlike a notes-to-backlog workflow, the prototype already answers "what does it look like" — the job here is to answer "what does it do" for every state and user type, not just the happy path.

---

## Prerequisites

- [ ] Figma link, image exports, or screen recording of the prototype
- [ ] Access to Claude (vision), ChatGPT-4V, or similar multimodal LLM
- [ ] Designer available for 30-minute clarification session (optional but recommended)

---

## Step 1: Document the User Journey

Before analyzing individual screens, map the full user journey the prototype covers.

**Prompt:**
```
I'm going to describe a prototype for [feature name]. Based on this description, map out the complete user journey — from entry point to completion — including:

1. Entry points (how does the user arrive at this feature?)
2. Primary flow (the happy path, step by step)
3. Alternative flows (what choices can the user make?)
4. Exit points (where does the user go after completing the flow?)
5. Error states visible in the prototype

Feature description:
[describe the feature or paste designer notes]

Screens in the prototype (list by name):
[list screen names]
```

This map becomes your story grouping framework.

---

## Step 2: Analyze Each Screen

For each screen in the prototype, extract the requirements:

**Prompt (with image or description):**
```
Analyze this screen from a product requirements perspective.

For this screen, identify:
1. What user action(s) can be performed?
2. What data is displayed? (field names, formats, states)
3. What triggers this screen to appear?
4. What happens after each possible action?
5. What states does this screen have? (empty, loading, error, success, read-only)
6. What validation occurs on user input?
7. What permissions or roles affect what's shown?
8. What is NOT shown in this screen that a developer would need to know?

Screen name: [name]
[attach image or paste description]
```

Collect the output for all screens before generating stories.

---

## Step 3: Identify Missing States

Prototypes often show only the happy path. Before writing stories, identify what's missing:

**Prompt:**
```
Based on the following screen analysis for [feature name], identify interaction states that are NOT shown in the prototype but must be defined before development:

Common missing states:
- Empty state (no data to display)
- Loading state (async operation in progress)
- Error state (API failure, validation failure, permission denied)
- Partial state (some data loaded, some pending)
- Mobile/responsive behavior
- Accessibility requirements (keyboard nav, screen reader labels)
- Permission-based variations (what changes for different user roles?)

Screen analyses:
[paste Step 2 output]
```

Add the missing states to your story acceptance criteria. This is where most QA bugs originate.

---

## Step 4: Identify Backend Requirements

PMs often write frontend-only stories that surprise engineering with implicit backend work. Catch this early:

**Prompt:**
```
Based on the following screen analyses, identify the backend requirements implied by the UI:

1. Data that must be fetched (API endpoints needed)
2. Data that must be created, updated, or deleted
3. Business rules that must be enforced server-side
4. Notifications or side effects triggered by user actions
5. Permission checks that must happen server-side
6. Data that must persist across sessions

Screen analyses:
[paste Step 2 output]
```

These become backend stories or acceptance criteria on existing stories.

---

## Step 5: Generate Epics

Group the screens and flows into epics. Each epic should represent a coherent user capability — not a screen, not a technical layer.

**Prompt:**
```
Based on the following prototype analysis for [feature name], generate epics that group related screens and user capabilities.

Each epic should:
- Represent a coherent user capability (not a screen or a layer)
- List which screens from the prototype fall in scope
- Reference missing states in Functional Expectations ("The system shall display an empty state when...")
- Include backend requirements in Constraints and Assumptions
- Flag any undecided design as [To validate]

User journey map: [paste Step 1 output]
Screen analyses: [paste Step 2 output]
Missing states: [paste Step 3 output]
Backend requirements: [paste Step 4 output]
```

See full format: [generate-epics.md](../prompts/generate-epics.md)

---

## Step 5b: Resolve [To Validate] Assumptions

**Do not generate stories until this step is complete.**

Review each epic for items marked `[To validate]` in Section 8. These often represent design decisions that were deferred ("we'll figure that out later"). Each one affects story scope or acceptance criteria.

For each `[To validate]` item:
1. Answer it now if the design decision has been made
2. If it requires a designer decision, flag it as a **Designer Review Item** and schedule it before story generation
3. Update the epic with the resolved value

---

## Step 6: Generate User Stories

For each epic, generate stories grouped by user journey stage:

**Prompt:**
```
Based on the following prototype analysis for [feature name], generate user stories.

Group stories by user journey stage:
- Setup / Onboarding (if applicable)
- Core happy path
- Alternative flows
- Error and edge cases
- Admin / management views (if applicable)

For each story:
- Reference the specific screen(s) it covers in the Context paragraph
- Include acceptance criteria that reference UI states (empty, loading, error)
- Call out any backend requirement implied by the UI as an AC item
- Note if a design spec is needed beyond the prototype

User journey map: [paste Step 1 output]
Screen analyses: [paste Step 2 output]
Missing states identified: [paste Step 3 output]
Backend requirements: [paste Step 4 output]
```

See full prompt: [generate-user-stories.md](../prompts/generate-user-stories.md) — "Stories From Prototype Analysis" variant.

---

## Step 7: Validate With Designer

Schedule a 30-minute review with the designer to confirm:

- [ ] Stories correctly capture what was designed
- [ ] Missing states are handled as the designer intended
- [ ] Any design debt or "we'll figure that out later" items are flagged
- [ ] Responsive/mobile behavior is documented
- [ ] Component library compliance is noted (or custom components flagged as TSK items)

Update stories based on feedback before engineering review.

---

## Step 8: Engineering Feasibility Check

Before finalizing, run a quick feasibility pass with a senior engineer:

1. Are there any screens that will be significantly harder to build than they look?
2. Are there backend requirements we missed?
3. Are there existing components or patterns we should reuse?
4. Are there performance concerns with any of the data displayed?

Document the answers in the relevant stories.

---

## Step 9: Evaluate and Refine

Run each batch through quality evaluation before sprint planning.

See [04-story-evaluation.md](../workflows/04-story-evaluation.md)

---

## Step 10: Dev-Ready Handoff

Before moving items into a sprint:
- [ ] All stories pass quality evaluation (grade ≥ 9.0)
- [ ] Figma links attached to each story (engineers should never hunt for the design)
- [ ] Missing states covered in AC
- [ ] Backend requirements documented as AC or separate items
- [ ] Designer review completed
- [ ] Engineering feasibility confirmed

See [05-dev-ready-handoff.md](../workflows/05-dev-ready-handoff.md)

---

## Estimated Time Per Step

| Step | Time |
|---|---|
| Document user journey | 20 min |
| Analyze screens | 30 min |
| Identify missing states | 15 min |
| Identify backend requirements | 15 min |
| Generate epics | 20 min |
| Resolve assumptions | 20 min |
| Generate stories | 20 min |
| Designer review | 30 min |
| Engineering feasibility | 20 min |
| Evaluate and refine | 20 min |
| **Total** | **~3 hours** |

---

## Output Checklist

```
[ ] User journey map documented
[ ] All screens analyzed
[ ] Missing states identified and added to acceptance criteria
[ ] Backend requirements documented
[ ] [To validate] assumptions resolved before story generation
[ ] Stories generated, covering happy path + alternatives + errors + missing states
[ ] Designer review completed
[ ] Engineering feasibility confirmed
[ ] Figma links attached to all stories
[ ] Stories imported to backlog tool
```

---

## Pro Tips

- **Attach Figma links directly to stories.** Engineers should never hunt for the design.
- **Annotate the prototype, not just the ticket.** Add dev notes to Figma frames so context lives in both places.
- **One story per screen is a bad heuristic.** Group by user action, not by screen.
- **Design system debt is a story.** If a screen requires a new component, create a TSK for it.
- **"We'll design that later" is a blocker.** Flag undefined states before engineering starts — they become `[To validate]` items in the epic.

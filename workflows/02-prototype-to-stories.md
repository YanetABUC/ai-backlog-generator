# Workflow 02: Prototype to Stories

**Time:** 1–3 hours  
**Input:** Figma prototype, wireframes, clickable mockup, or screen recordings  
**Output:** User stories with visual context embedded

---

## Overview

Prototypes are underused as backlog inputs. They contain implicit requirements that designers understand but rarely document — interaction states, empty states, error states, loading states, transitions. This workflow systematically extracts all of that into dev-ready stories.

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

## Step 5: Generate Stories From Prototype Analysis

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
- Reference the specific screen(s) it covers
- Include acceptance criteria that reference UI states (empty, loading, error)
- Call out any backend requirement implied by the UI
- Note if a design spec is needed beyond the prototype

User journey map: [paste Step 1 output]
Screen analyses: [paste Step 2 output]
Missing states identified: [paste Step 3 output]
Backend requirements: [paste Step 4 output]
```

---

## Step 6: Validate With Designer

Schedule a 30-minute review with the designer to confirm:

- [ ] Stories correctly capture what was designed
- [ ] Missing states are handled as the designer intended
- [ ] Any design debt or "we'll figure that out later" items are flagged
- [ ] Responsive/mobile behavior is documented
- [ ] Component library compliance is noted (or custom components flagged)

Update stories based on feedback before engineering review.

---

## Step 7: Engineering Feasibility Check

Before finalizing, run a quick feasibility pass with a senior engineer:

Questions to answer:
1. Are there any screens that will be significantly harder to build than they look?
2. Are there any backend requirements we missed?
3. Are there existing components or patterns we should reuse?
4. Are there performance concerns with any of the data displayed?

Document the answers in the relevant stories.

---

## Output Checklist

```
[ ] User journey map documented
[ ] All screens analyzed
[ ] Missing states identified and added to acceptance criteria
[ ] Backend requirements documented
[ ] Stories generated, covering happy path + alternatives + errors
[ ] Designer review completed
[ ] Engineering feasibility confirmed
[ ] Stories imported to backlog tool with Figma links attached
```

---

## Pro Tips

- **Attach Figma links directly to stories.** Engineers should never hunt for the design.
- **Annotate the prototype, not just the ticket.** Add dev notes to Figma frames so context lives in both places.
- **One story per screen is a bad heuristic.** Group by user action, not by screen.
- **Design system debt is a story.** If a screen requires a new component, create a story for it.
- **"We'll design that later" is a blocker.** Flag undefined states before engineering starts.

# Prompt Evaluation Framework

Not all AI-generated backlog content is equal. This framework helps you assess whether the output from any AI prompt is reliable enough to use, refine, or discard.

---

## Why Evaluate AI Output

AI is confident even when it's wrong. It will generate plausible-sounding stories that miss the real business goal, acceptance criteria that sound testable but aren't, and epics that fragment value in ways that don't map to how users actually work.

Evaluation is the human layer that makes AI output safe to ship.

---

## The Three Levels of Evaluation

### Level 1: Structural Check (30 seconds)

Does the output have the right shape?

```
[ ] Contains all required fields (title, user, value, criteria)
[ ] Format matches the template
[ ] No placeholder text ("insert user here", "TBD")
[ ] No content that is obviously hallucinated or off-topic
```

If any of these fail, regenerate with a more constrained prompt before going further.

---

### Level 2: Content Quality Check (2–5 minutes)

Is the content accurate and complete?

**For Epics:**
- Does the epic represent a meaningful product capability?
- Is the user segment real and specific?
- Is the business goal measurable?
- Are the child stories implied correctly?

**For User Stories:**
- Is the persona specific to this product's user types?
- Is the action the user actually performs (not the system)?
- Is the value tied to a real outcome?
- Would this story survive a sprint review?

**For Acceptance Criteria:**
- Is each criterion independently testable?
- Is there a clear pass/fail state?
- Are error states covered?
- Are edge cases represented?

---

### Level 3: Business Context Check (5–10 minutes)

Does the output reflect actual product knowledge?

This is the most important check and the one AI cannot do alone. It requires you — the PM or BA — to validate:

1. **Does this match what users actually do?** AI generates based on patterns, not your specific user research. Cross-check against interview data, analytics, or support tickets.

2. **Does this reflect technical reality?** AI doesn't know your architecture. A story that sounds correct may be technically infeasible given your stack, data model, or existing constraints.

3. **Does this align with the roadmap?** AI generates what's logical. You know what's prioritized, what's been deprioritized, and what's a dependency for something else.

4. **Does this introduce scope creep?** AI tends toward completeness. Flag any generated content that represents future scope, not current sprint scope.

---

## Evaluating Prompt Performance

Track which prompts produce reliably good output. Use this scorecard:

| Dimension | Score (1–3) | Notes |
|---|---|---|
| Relevance — output matches the intent | | |
| Completeness — all required parts present | | |
| Specificity — concrete, not generic | | |
| Accuracy — no hallucinated context | | |
| Usability — minimal editing needed | | |

**Score 13–15:** Prompt is production-ready. Use as-is.
**Score 9–12:** Prompt is good but needs context improvement.
**Score below 9:** Prompt needs redesign — add constraints, examples, or role.

---

## Common Failure Modes and Fixes

### Failure: Output is too generic
AI generates "As a user, I want to view my profile" instead of domain-specific stories.

**Fix:** Add persona definitions and domain context to the system prompt or as a preamble.

```
Context: This is a B2B SaaS platform for warehouse operations. Users are warehouse managers, pickers, and logistics coordinators. Avoid generic personas.
```

---

### Failure: Acceptance criteria are vague
AI generates "The system should respond quickly" instead of measurable criteria.

**Fix:** Add explicit instructions to use Given/When/Then format and include specific thresholds.

```
All acceptance criteria must use Given/When/Then format. If a performance criterion is included, specify a measurable threshold (e.g., "under 300ms", "fewer than 3 clicks").
```

---

### Failure: Stories are too large
AI generates a story that covers an entire feature in one ticket.

**Fix:** Add a size constraint and examples of correctly-sized stories.

```
Each story should represent 1–3 days of work for a mid-level engineer. If a story touches more than 2 system layers or requires more than one PR, it is too large. Split it.
```

---

### Failure: Edge cases are missing
AI generates only the happy path.

**Fix:** Use the [identify-edge-cases prompt](../prompts/identify-edge-cases.md) as a separate step after story generation.

---

### Failure: Output doesn't reflect the codebase
AI generates stories that conflict with existing architecture or duplicate existing functionality.

**Fix:** Include relevant codebase context. See [handoff-from-codebase.md](../docs/handoff-from-codebase.md).

---

## Evaluation Automation

For teams running AI evaluation at scale, consider:

1. **AI-assisted evaluation:** Use the [evaluate-story-quality prompt](../prompts/evaluate-story-quality.md) to have a second AI pass evaluate the first AI's output.
2. **Evaluation template:** Use the [evaluation template](../templates/evaluation-template.md) to standardize scoring.
3. **Definition of Ready gate:** Block stories from entering the sprint board until they pass the quality checklist.

---

## Prompt Iteration Protocol

When a prompt produces poor output:

1. Identify which Level (1, 2, or 3) the failure occurred at.
2. Add one specific constraint to the prompt to address that failure.
3. Regenerate and compare.
4. Repeat until the output consistently meets Level 2 quality.
5. Document the improved prompt in the [prompts/](../prompts/) directory.

Never make multiple changes to a prompt at once — you won't know which change fixed it.
# Prompt: Semantic Review

Use this prompt to surface meaning-level risks in a backlog item: claims that can't be traced to validated requirements, AC that don't actually prove the user goal, BDD scenarios disconnected from AC, contradictions across stories, and inferences stated as facts.

Structural evaluation checks form. Semantic review checks whether the content is logically sound and traceable.

---

## When to Use

- After a story passes structural evaluation (grade ≥ 7.0 on the 8-dimension rubric)
- Before engineering handoff or sprint planning
- When a story "looks fine" but something feels off
- When reviewing stories inherited from another team or tool
- Whenever a story contains a lot of behavioral detail that wasn't explicitly discussed in discovery

---

## Core Prompt

```
You are a senior business analyst performing a semantic review of a user story. Your job is not to check formatting or structure — that has already been done. Your job is to check whether the content is logically sound, fully traceable to validated requirements, internally consistent, and free from unvalidated inferences.

You will produce a risk register: a list of specific claims that require human verification before this story can be handed to engineering. This is not a score — it is a list of questions only a PM or domain expert can answer.

---

## Five Checks to Perform

### Check 1 — Claim Traceability

For each Acceptance Criteria item, determine whether the claimed behavior is traceable to one of:
- A problem, pain point, or goal stated in the discovery context provided below
- A functional expectation listed in the parent epic provided below
- An assumption that was explicitly validated (not listed as open or unknown)

Flag any AC item that introduces a behavioral claim with no traceable source. These are inferences made during writing — they may be correct, but they are unconfirmed and will cause rework if they turn out to be wrong.

### Check 2 — Goal-AC Alignment

Read the "so that" clause. Test:
- Necessity: does every AC item contribute to achieving that goal? Flag any AC that doesn't.
- Sufficiency: if every AC passes, would the user goal actually be achieved? Flag any condition required by the goal that no AC covers.

A story where all ACs pass but the goal is not delivered is the most dangerous quality failure — it ships and creates no value.

### Check 3 — BDD-AC Alignment

For each BDD scenario, identify which AC item it exercises.
- Flag any BDD scenario testing behavior not stated in any AC item (orphaned scenario)
- Flag any AC item with no corresponding BDD scenario (untested condition)

### Check 4 — Cross-Story Consistency

Compare the story under review against the sibling stories provided below.
- Flag any contradiction in persona descriptions, permissions, thresholds, or behavioral rules between this story and a sibling
- Flag any business term used with an inconsistent meaning across stories

### Check 5 — Inference Audit

Read the full story — narrative, AC, BDD scenarios, NFRs — and identify any claim that is a deduction rather than a stated requirement. Look for:
- Behavioral preferences not backed by user research
- Outcome predictions not from validated data
- Technical assumptions stated as requirements
- Missing-state behaviors invented without a stated rule
- Scope assumptions drawn from adjacent context

Flag each inference with the exact sentence and the question that would validate it.

---

## Output Format

### High Risk — Resolve Before Handoff

[SR-H1] {Short label}
- Found in: [location in story]
- Issue: [what the claim says and why it is risky]
- Verify: [exact question a PM/BA must answer]

### Medium Risk — Confirm Before Sprint

[SR-M1] {Short label}
- Found in: [location]
- Issue: [description]
- Verify: [question]

### Low Risk — Worth Noting

[SR-L1] {Short label}
- Found in: [location]
- Issue: [description]
- Verify: [question]

### Checks Passed

List any of the five checks that produced no findings.

### Questions to Verify

Numbered list of every question from all risk items — formatted for copy-paste into a stakeholder review or team sync.

### Verdict

Proceed to handoff: Yes (no High risks) / No (resolve High risks first)

---

## Story Under Review

[paste full story here]

---

## Parent Epic

[paste the epic's Narrative, Functional Expectations, and Constraints sections here]

---

## Discovery Context

[paste the Problem Statement, Gap Analysis, and any validated/invalidated assumptions here — or write "not available"]

---

## Sibling Stories (same epic)

[paste titles and AC sections of other stories in this epic — or write "not available"]
```

---

## Variant: Quick Inference Scan

For a fast check focused only on unvalidated inferences — useful during review or grooming:

```
Read the following user story and identify every claim that appears to be an inference rather than a stated requirement.

An inference is any behavioral assertion, user preference, outcome prediction, default behavior, or scope assumption that does not trace directly to a user research finding, a validated business rule, or an explicitly stated requirement.

For each inference found:
1. Quote the exact sentence
2. Explain why it appears to be inferred rather than stated
3. Write the question that would validate or invalidate it

Story:
[paste]

Known validated requirements and discovery context:
[paste or write "not available"]
```

---

## Variant: Cross-Story Consistency Check

For checking a batch of stories in the same epic against each other:

```
Review the following user stories from the same epic and identify any contradictions in:
- Persona descriptions, roles, or permissions
- Behavioral rules or thresholds (e.g., approval limits, access rules, data visibility)
- Domain term definitions (e.g., "pending" meaning different states in different stories)
- Scope assumptions (e.g., one story implies a feature exists that another story says is out of scope)

For each contradiction found:
- Name the two stories in conflict
- Quote the conflicting statements from each
- State which one should be authoritative and why, or flag it as unresolved

Stories:
[paste all stories in the epic]
```

---

## What This Does Not Replace

Semantic review surfaces risks that require human judgment to resolve. It does not replace:

- **Structural evaluation** — run `/backlog:evaluate-item` or [evaluate-story-quality.md](evaluate-story-quality.md) first
- **Stakeholder validation** — the questions in the risk register must be answered by a PM, domain expert, or user researcher, not by re-running the prompt
- **Engineering feasibility review** — semantic review does not catch technical constraints

The correct sequence is: structural evaluation → semantic review → resolve flagged questions → dev-ready handoff.

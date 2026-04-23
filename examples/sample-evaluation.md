# Sample Story Evaluation

**Product:** RetailOps — Vendor Replenishment Automation  
**Batch:** Epic 2 - PO Suggestion Management (first draft, before refinement)  
**Evaluator:** AI-assisted, reviewed by PM  
**Date:** Q2 2025

---

## Stories Evaluated (First Draft — Pre-Refinement)

This document shows the evaluation of three stories as they were initially drafted, before quality review. The purpose is to demonstrate the evaluation process and how specific fixes improve scores.

---

## Story A (First Draft): View PO suggestions

> As a user, I want to see purchase order suggestions so that I know what to order.

### Evaluation

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | 1/3 | "User" is too generic — no role, no context, no implied goal |
| Business Value | 1/3 | "Know what to order" is not measurable — what decision does this enable? |
| AC Quality | 1/3 | No acceptance criteria provided |
| Story Size | 2/3 | Difficult to estimate without AC — probably scoped reasonably but unclear |
| Edge Cases | 1/3 | No criteria = no edge cases |
| Dependencies | 1/3 | No dependencies mentioned — unclear what data source populates suggestions |
| **Total** | **7/18** | |

**Status:** ❌ Requires Rework

**Top 2 fixes:**
1. Replace "user" with: "As a warehouse manager responsible for daily inventory replenishment at a single location..." and tie the outcome to: "...so that I can review and act on them before the vendor order cutoff time"
2. Add at least 5 Given/When/Then acceptance criteria covering: what's shown in the list, the empty state, the loading state, the location filter behavior, and the click-through to suggestion details

---

## Story B (First Draft): Approve a suggestion

> As a warehouse manager, I want to approve purchase order suggestions so that POs get created.

### Evaluation

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | 2/3 | Role is correct but no context — when does this happen? What's their goal? |
| Business Value | 1/3 | "So that POs get created" describes a system action, not a user outcome |
| AC Quality | 1/3 | No acceptance criteria |
| Story Size | 3/3 | Approval action is appropriately scoped |
| Edge Cases | 1/3 | No criteria = no edge cases |
| Dependencies | 1/3 | PO threshold routing not mentioned; document generation dependency missing |
| **Total** | **9/18** | |

**Status:** ❌ Requires Rework

**Top 2 fixes:**
1. Improve "so that" clause: "...so that a PO document is immediately generated and I can move on to the next item without manual data entry"
2. Add acceptance criteria that cover: the confirmation dialog, the <$5K vs ≥$5K routing behavior, the post-approval queue behavior, and the API failure case

---

## Story C (First Draft): Generate a PO suggestion automatically

> As a system, I want to create PO suggestions when stock is low so that managers don't have to check manually.

### Evaluation

| Dimension | Score | Issue |
|---|---|---|
| User Clarity | 1/3 | "As a system" is not a valid user story persona — this is a technical story framed incorrectly |
| Business Value | 2/3 | "Managers don't have to check manually" is close but should be quantified |
| AC Quality | 1/3 | No acceptance criteria |
| Story Size | 2/3 | Likely M — hard to judge without AC |
| Edge Cases | 1/3 | Duplicate suggestion prevention not mentioned; SKU without vendor not addressed |
| Dependencies | 1/3 | Real-time stock API dependency not mentioned; required SKU fields not listed |
| **Total** | **8/18** | |

**Status:** ❌ Requires Rework

**Top 2 fixes:**
1. Reframe as a user story from the manager's perspective: "As a warehouse manager, I want the system to automatically create a purchase order suggestion when a SKU's stock falls below its configured reorder point so that I don't need to manually monitor stock levels..."
2. Add acceptance criteria for: the trigger condition, the suggestion contents, the duplicate prevention logic, the no-vendor edge case, and the no-reorder-point edge case

---

## Post-Refinement Comparison

After applying fixes (see [before-and-after-story.md](before-and-after-story.md)):

| Story | Before | After | Delta |
|---|---|---|---|
| Story A (View PO queue) | 7/18 | 17/18 | +10 |
| Story B (Approve suggestion) | 9/18 | 16/18 | +7 |
| Story C (Auto-generate suggestion) | 8/18 | 16/18 | +8 |

**Common issues across all 3 stories:**
1. No acceptance criteria (biggest single gap)
2. Generic or incorrect user persona
3. Dependencies not identified

**Root cause:** Stories were written quickly from bullet points without using a structured story template. Adding a template requirement to the team's story creation process would prevent these gaps.

---

## Recommendations

1. **Enforce story template before grooming** — Stories without the full template structure (user, value, AC, size, dependencies) should not enter grooming.
2. **Add AC generation as a step** — After writing the story narrative, run the [generate-acceptance-criteria prompt](../prompts/generate-acceptance-criteria.md) before submitting for review.
3. **Run evaluation on every batch** — Use the [evaluate-story-quality prompt](../prompts/evaluate-story-quality.md) before grooming sessions, not during them.

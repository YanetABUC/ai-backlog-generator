# Workflow 06: Market Analysis to Backlog

**Time:** 2–4 hours  
**Input:** Competitor research, user reviews, pricing data, analyst reports, demo notes  
**Output:** Prioritized gap analysis + decision to write new epics or refine existing ones

---

## Overview

This workflow turns competitive market research into backlog decisions. Unlike discovery workflows that start from your own product's problems, this one starts from what the market reveals — what competitors do, what users say, and where no one is serving a segment well.

The output is not automatically a set of new epics. It is a structured analysis that you review and act on: you decide whether the gaps justify new epics, refinements to existing ones, or simply saving the analysis as context for later.

**Claude Code:** `/backlog:market-analysis`

---

## Prerequisites

Before starting:
- [ ] A clear product to analyze (name, category, what it does today)
- [ ] At least 2 competitors identified
- [ ] At least one research source per competitor: user reviews (G2, Capterra, App Store, Reddit), pricing page, demo recording, analyst report, or interview notes
- [ ] A decision you are trying to inform (build new capability, improve existing, validate direction, reposition)

---

## Step 1: Gather Research

Collect raw research inputs into a single document. Include:
- User reviews verbatim — especially quotes that name specific pain points or praise
- Pricing page screenshots or notes (tier names, feature gating, price points)
- Demo or feature comparison notes (what you saw, what was missing)
- Analyst or article excerpts if available
- Any user interview notes where competitors were mentioned

**Do not filter or summarize yet.** Raw, messy input produces better analysis than pre-processed summaries because AI can extract patterns you might miss.

---

## Step 2: Define the Analysis Scope

Before running the analysis, answer these questions:

1. What is the product we are analyzing, and what does it do today?
2. Which competitors are we comparing against? (up to 5)
3. Who are the target users? (specific roles and context — not "users" or "customers")
4. What decision are we trying to make with this research?

Write these down — they become the input frame for the analysis.

---

## Step 3: Run the Competitive Analysis

**Prompt:**

Use the Core Prompt from [market-analysis.md](../prompts/market-analysis.md).

Paste your research in the "Research available" field. Include the decision from Step 2 in "Decision to inform."

The output will be three structured sections:
- **Capability Comparison** — feature-by-feature table with Strong / Partial / Missing / Unknown
- **User Perception** — what real users praise and complain about per product
- **Positioning and Pricing** — target segments, price models, differentiators, weaknesses

Review all three sections before proceeding. Correct anything the AI got wrong. Add any nuance it missed.

---

## Step 4: Identify and Prioritize Strategic Gaps

From the analysis, the AI will surface three types of gaps:

| Gap Type | What it means |
|---|---|
| **Opportunity gap** | A user pain point no competitor solves well — differentiation potential |
| **Capability gap** | Something competitors do that we lack — risk if left unaddressed |
| **Positioning gap** | A user segment or use case no one targets clearly — market white space |

For each gap, assign a priority:
- **High** — affects your target users directly, has evidence from real user data, and is feasible to address
- **Medium** — worth tracking; address in a future phase or as part of a larger epic
- **Low** — interesting signal but not actionable now

**Prompts to help:**

*Group gaps before prioritizing (if list is long):*
```
Group the identified gaps by theme. For each theme, name the theme and list the gaps that belong to it.
```

*Prioritize within a theme:*
```
For each High-priority gap, confirm: (1) which target users it affects, (2) what the evidence is, and (3) what the risk is if we don't address it.
```

---

## Step 5: Human Review Gate

**Stop here.** Do not generate or modify epics until you have reviewed the analysis and decided on an action.

Review:
- [ ] Does the capability comparison reflect your actual product state?
- [ ] Are the user perception summaries grounded in real sources?
- [ ] Are the High-priority gaps ones your team can and should address?
- [ ] Does the decision you set in Step 2 have a clear answer now?

Decide on one of three actions:

| Action | When to choose |
|---|---|
| **Write new epics** | High-priority gaps represent capabilities your product doesn't have yet |
| **Refine existing epics** | High-priority gaps reveal your current epics are scoped incorrectly, missing NFR benchmarks, or missing competitive context |
| **Save only** | The analysis confirms your current direction — no epics need to change right now |

---

## Step 6a: Write New Epics

If your decision is to write new epics, use the "Generate Epics From Market Gaps" variant from [market-analysis.md](../prompts/market-analysis.md).

Paste the prioritized High-priority gaps as input.

Each generated epic will:
- Lead Strategic Context with the competitive gap that motivates it
- Reference competitor evidence in NFRs as a quality benchmark
- Flag unvalidated assumptions as `[To validate]`

**After generating epics:**

See full prompt: [generate-epics.md](../prompts/generate-epics.md)

---

## Step 6b: Resolve [To Validate] Assumptions

**Do not generate stories until this step is complete.**

Review each new epic for `[To validate]` items in Section 8 (Constraints and Assumptions). Each one represents a decision that affects story scope or feasibility.

For each `[To validate]` item:
1. Answer it now if you have the information
2. If not, add it to your open questions list and get the answer before writing stories
3. Update the epic with the resolved value

**Prompt:**
```
Review the following epic and list every item marked [To validate] in Section 8.
For each one, ask me the question I need to answer to resolve it.

Epic:
[paste epic]
```

---

## Step 7a: Refine Existing Epics

If your decision is to refine existing epics, use the "Refine Existing Epics With Competitive Context" variant from [market-analysis.md](../prompts/market-analysis.md).

Paste the analysis findings and the epics you want to update.

The AI will propose changes to specific sections only — Strategic Context, NFRs, Constraints, and Success Metrics. Review the proposed diff and confirm before applying.

---

## Step 8: Generate User Stories

For each epic (new or refined), generate backlog items once all assumptions are resolved.

See [generate-user-stories.md](../prompts/generate-user-stories.md)

When generating stories from market-based epics, the competitive context in the epic should inform:
- AC items that verify the capability gap is actually closed (not just implemented)
- NFR thresholds tied to competitor benchmarks
- Edge cases drawn from competitor failure scenarios users reported

---

## Step 9: Evaluate and Refine Stories

Run each batch through quality evaluation before sprint planning.

See [04-story-evaluation.md](../workflows/04-story-evaluation.md)

---

## Step 10: Dev-Ready Handoff

Before moving items into a sprint:
- [ ] All stories pass quality evaluation (grade ≥ 9.0)
- [ ] Dependencies identified and linked
- [ ] Engineers briefed on the competitive context — they need to know why the quality bar is set where it is
- [ ] Acceptance Criteria cover happy path, error states, and competitive differentiator scenarios

See [05-dev-ready-handoff.md](../workflows/05-dev-ready-handoff.md)

---

## Estimated Time Per Step

| Step | Time |
|---|---|
| Gather research | 30–60 min |
| Define scope | 10 min |
| Run competitive analysis | 20 min |
| Identify and prioritize gaps | 30 min |
| Human review gate | 20 min |
| Write or refine epics | 30 min |
| Resolve assumptions | 20 min |
| Generate stories (per epic) | 20 min |
| Evaluate and refine stories | 20 min |
| Dev-ready handoff | 15 min |
| **Total** | **~3.5–4 hours** |

---

## Output Checklist

```
[ ] Competitive analysis completed (capability, perception, positioning)
[ ] Strategic gaps identified and prioritized (High / Medium / Low)
[ ] Decision made: new epics / refine existing / save only
[ ] New epics generated OR existing epics updated
[ ] All [To validate] assumptions resolved before story generation
[ ] User stories generated for High-priority epics
[ ] All stories evaluated and scored ≥ 9.0
[ ] Engineering team briefed on competitive context
```

---

## Choosing This Workflow vs. Others

| If you have... | Use |
|---|---|
| Competitor research and market data | This workflow (06) |
| Raw discovery notes or a product brief | [Workflow 01](../workflows/01-discovery-to-backlog.md) |
| An existing product with known gaps | [Workflow 03](../workflows/03-codebase-to-backlog.md) |
| Existing stories needing review | [Workflow 04](../workflows/04-story-evaluation.md) |
| Stories ready for sprint | [Workflow 05](../workflows/05-dev-ready-handoff.md) |

Market analysis works well in combination with Workflow 01 or 03 — run them first to establish the internal view, then run this one to pressure-test it against the market.

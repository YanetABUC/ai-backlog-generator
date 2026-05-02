# Prompt: Market Analysis

Use this prompt to turn competitive research, user reviews, and market data into a structured gap analysis you can act on. Use the variants to generate new epics from identified gaps or to refine existing epics with competitive context.

---

## When to Use

- Before starting a new initiative and you want to validate the opportunity against the market
- When deciding whether to build a new capability or improve an existing one
- When stakeholders ask "how do we compare to [competitor]?"
- After gathering competitor reviews, pricing data, demo notes, or analyst reports
- When existing epics need to be sharpened with real competitive pressure

---

## Core Prompt: Competitive Analysis

```
You are a senior product strategist. I'm going to give you market research about a product and its competitors. Structure the research into a competitive analysis that identifies actionable gaps.

## Analysis Structure

Produce three sections:

### 1. Capability Comparison
Map capabilities across the key functional areas that matter to the target users. Present as a table:

| Capability Area | Our Product | [Competitor A] | [Competitor B] | [Competitor C] |
|---|---|---|---|---|
| [Area] | Strong / Partial / Missing / Unknown | ... | ... | ... |

Derive the functional areas from the research — use the ones that actually matter to the target users, not generic categories.

### 2. User Perception
Summarize what real users say — not marketing copy. For each product:
- Users praise: [specific things]
- Users complain about: [specific things]
- Common reason for switching: [if mentioned]

### 3. Positioning and Pricing
| Product | Target Segment | Pricing Model | Key Differentiator | Notable Weakness |
|---|---|---|---|---|
| [Product] | ... | ... | ... | ... |

## Strategic Gaps

From the analysis, identify three types of gaps:

**Opportunity gaps** — pain points no competitor solves well (where we could differentiate):
- [Gap] — affects [user role] — evidence: [source]

**Capability gaps** — things competitors do that we currently lack:
- [Gap] — [competitor] does this — risk if unaddressed: [impact]

**Positioning gaps** — segments or use cases no one targets clearly:
- [Segment/use case] — underserved because [reason]

Present all gaps and ask: which should be marked High / Medium / Low priority?

## CRITICAL CONSTRAINTS
- Draw conclusions only from the research provided — do not invent features or capabilities
- Use the language users actually use — not marketing terms
- Evidence must be traceable to a source (review site, interview, pricing page)
- Capability status must be "Unknown" if the research doesn't confirm it

## Input

**Product:** [name and what it does today]
**Competitors:** [name and one-line description each — up to 5]
**Target users:** [roles and context]
**Decision to inform:** [what choice this research supports]
**Research available:** [paste or describe: user reviews, pricing pages, demo notes, analyst reports, support tickets, interview summaries]
```

---

## Variant: Generate Epics From Market Gaps

Use this after completing the core analysis and prioritizing gaps. Paste the prioritized gap list as input.

```
You are a senior product manager generating epics from a competitive market analysis.

For each High-priority gap, generate one epic following this format:

---
# Epic: [Short Title]

## 1. Narrative (Big Cinematic Story)
**In order to** [strategic business outcome — often: win a market segment, close a competitive gap],
**As a** [primary user — specific role and context],
**I want to** [the high-level capability this epic delivers],
**So that** [measurable transformation in the user's experience or work].

## 2. Strategic Context
**Current situation:** [how the user copes today — including what competitors offer]
**Core problems:** [what competitors fail to solve, from real user evidence]
**Competitive reference:** [which competitor does this, what users say about it]
**Business impact of the gap:** [risk of not closing this gap — user churn, lost segment]
**Why now:** [market signal that makes this urgent]

## 3. Desired Transformation
**What this epic will enable:** [future state]
**Shift from → to:** [table: From | To — specific pairs]

## 4. Scope Definition
### In Scope
[Capabilities that close the identified gap]
### Out of Scope
[Capabilities that belong in a later phase or are out of scope]

## 5. Target Users and Actors
[Primary beneficiaries with role, context, and expected benefit]

## 6. High-Level Functional Expectations
[The system shall... — observable behaviors, not implementation]

## 7. Non-Functional Expectations
[Use competitor benchmarks as the baseline where relevant — e.g. "must match or exceed [competitor]'s load time of X seconds"]

## 8. Constraints and Assumptions
[Competitive constraints + flag unvalidated assumptions as [To validate]]

## 9. Dependencies
[Internal and external dependencies]

## 10. Success Metrics
[Business and operational metrics — use competitor benchmarks as the baseline where possible]
---

## CRITICAL CONSTRAINTS
- Strategic Context must reference the competitive gap that motivates this epic
- NFRs may cite competitor benchmarks as the quality bar
- Never include architecture decisions, API specs, or technical design
- Every unvalidated assumption must be marked [To validate]
- Out of Scope must be explicit

## Input

**High-priority gaps:**
[paste the prioritized opportunity and capability gaps from the core analysis]

**Target users:**
[paste from the analysis]
```

---

## Variant: Refine Existing Epics With Competitive Context

Use this when you have existing epics and want to update them based on what the market analysis revealed.

```
You are a senior product manager. I have existing epics that need to be updated with competitive market context.

For each epic I provide, suggest specific updates to these sections only — do not rewrite the full epic:

**Strategic Context (Section 2):**
- Add "Competitive reference" if the gap maps to a competitor advantage
- Sharpen "Why now" with market evidence from the analysis

**Non-Functional Expectations (Section 7):**
- Add benchmarks from competitors where the analysis shows a quality bar to meet or exceed

**Constraints and Assumptions (Section 8):**
- Add competitive risks or dependencies revealed by the analysis
- Flag any assumption invalidated by competitor behavior as [To validate]

**Success Metrics (Section 10):**
- Sharpen targets using competitor benchmarks as a measurable baseline

Present proposed changes as a diff per section — do not rewrite unchanged sections. Ask for confirmation before I apply them.

## Input

**Market analysis findings:**
[paste the capability comparison, user perception, and prioritized gaps]

**Epics to update:**
[paste each epic or list the IDs]
```

---

## Tips for Better Output

- More specific research = more specific gaps. "Users on G2 say X is slow" is more useful than "competitor X has performance issues"
- Name the decision you're trying to make upfront — it focuses the analysis on what actually matters
- Include pricing and positioning data even if it seems basic — AI uses it to identify segments that are underserved
- After generating epics from gaps, resolve every `[To validate]` assumption in Section 8 before writing user stories
- If the analysis surfaces too many gaps, ask AI to group them by theme before prioritizing — it makes the High/Medium/Low decision easier
- Use competitor NFR benchmarks as your quality bar in epics: "must load in under 2 seconds — matching [competitor]'s current performance"

# AI Backlog Generator

> A practical framework for generating, evaluating, and refining AI-assisted product backlogs — from discovery to dev-ready stories.

*By Yanet Morales Ramirez*

---

## AI-Native Backlog Generation

This repository documents how I approach backlog creation using AI — from early discovery and prototyping to development-ready handoff.

Instead of writing user stories in isolation, I combine:

- **Product discovery** — extract structure from raw notes, briefs, and research
- **Fast feedback loops** — validate assumptions before investing in story writing
- **Codebase analysis** — surface constraints, gaps, and existing behavior from the code itself
- **AI-assisted generation** — produce epics, stories, acceptance criteria, and BDD scenarios conversationally
- **Structured evaluation** — score every story against a quality bar before it enters a sprint

The goal is not just to generate backlog items, but to create high-quality, context-aware work that engineering teams can act on immediately.

---

## Why this matters

AI can generate user stories easily.

But most AI-generated backlogs:

- lack context
- ignore technical constraints
- miss edge cases
- create more confusion than clarity

This repository focuses on solving that problem by combining:

- AI generation
- human validation
- engineering context
- structured evaluation

to produce backlog items that are actually usable.

---

## Who This Is For

- Product Managers adopting AI-native workflows
- Business Analysts bridging discovery and delivery
- Engineering teams wanting dev-ready stories without constant back-and-forth
- Teams using AI-driven agile frameworks

---

## Getting Started — Load the Agent

The fastest way to use this framework is to load the master agent into your AI tool of choice. Once loaded, you interact conversationally — no copy-pasting prompts required.

### Option A: Claude Projects (recommended)
1. Open [claude.ai](https://claude.ai) → Create a new Project
2. Click **Set project instructions** → paste the contents of [`agent.md`](agent.md)
3. Add these files to **Project Knowledge** (optional but recommended):
   - [`templates/user-story-template.md`](templates/user-story-template.md)
   - [`templates/epic-template.md`](templates/epic-template.md)
   - [`docs/backlog-quality-criteria.md`](docs/backlog-quality-criteria.md)
   - [`examples/sample-user-stories.md`](examples/sample-user-stories.md)
4. Start a conversation — see examples below

### Option B: Custom GPT (ChatGPT)
1. Explore GPTs → Create → paste [`agent.md`](agent.md) into Instructions
2. Upload the template files to Knowledge
3. Share the GPT link with your team

### Option C: Cursor / VS Code
1. Copy the system prompt from [`agent.md`](agent.md) into `.cursorrules` in your project root
2. The agent is active in every AI conversation in that workspace

### Option D: Use Prompts Directly
If you prefer not to use an agent, all prompts are available individually in the [`prompts/`](prompts/) folder. Copy the relevant prompt into any AI chat session.

---

## How It Works — Conversational Examples

Once the agent is loaded, the experience is conversational. Here are real examples:

---

**Starting a new backlog from a brief:**

> **You:** I want to build a vendor replenishment automation feature for our warehouse management system. Warehouse managers spend 2–3 hours a day on manual stock reviews. I have a product brief.
>
> **Agent:** Got it — I'll use this as context for everything we generate. Paste your brief and I'll extract the problem statement, validate the key assumptions, and then generate epics. Want to run a quick feedback loop first to confirm direction, or jump straight to epics?

---

**Generating epics:**

> **You:** Generate epics from this brief. [paste brief]
>
> **Agent:** [generates 4 epics in the canonical 10-section format]
> Want me to RICE-score these so we can prioritize before writing stories?

---

**Writing stories:**

> **You:** Write stories for the first epic.
>
> **Agent:** [generates 5–8 stories in the canonical format, each with AC, BDD scenarios, FRs, NFRs, and Out of Scope]
> Should I evaluate these before we move on?

---

**Evaluating quality:**

> **You:** Evaluate these stories. [paste]
>
> **Agent:** [scores each story on 7 dimensions, flags specific issues, provides targeted fixes]
> Stories 1, 3, and 4 are dev-ready. Stories 2 and 5 need targeted fixes — want me to rewrite them now?

---

**Generating BDD scenarios:**

> **You:** Write BDD scenarios for US3.
>
> **Agent:** [generates Happy Path, Validation, Edge Case, and Failure Handling scenarios with AC traceability]
> All 5 AC items are covered. Want me to check for missing edge cases?

---

**Splitting a large story:**

> **You:** This story is too large. Split it. [paste]
>
> **Agent:** [splits into 3 sprint-sized stories with delivery order and full template sections per story]

---

**Finding edge cases:**

> **You:** What edge cases am I missing in this story?
>
> **Agent:** [identifies data boundaries, permission edge cases, concurrent access, system failures, and time-based conditions with severity ratings]

---

## The Artifact Hierarchy

This framework produces three artifact types, each with its own template:

```
Epic
 └── User Story
      ├── Acceptance Criteria  (checklist — conditions that must be true for "done")
      └── BDD Scenarios        (Given/When/Then — proof the criteria are met)
```

**Acceptance Criteria ≠ BDD Scenarios.**

| | Acceptance Criteria | BDD Scenarios |
|---|---|---|
| Format | Declarative checklist | Gherkin — Given/When/Then |
| Purpose | Define conditions for "done" | Prove those conditions in specific situations |
| Coverage | What must be true | Happy Path + Validation + Edge Case + Failure Handling |

---

## Repository Structure

```
ai-backlog-generator/
│
├── agent.md                               ← Load this into Claude, ChatGPT, or Cursor
├── README.md                              ← You are here
│
├── docs/
│   ├── overview.md                        ← How the full framework fits together
│   ├── backlog-quality-criteria.md        ← The 7-dimension quality bar for every story
│   ├── prompt-evaluation-framework.md     ← How to evaluate and improve AI output
│   ├── fast-feedback-loop.md              ← Validate assumptions before writing stories
│   └── handoff-from-codebase.md          ← Generate backlog from existing code
│
├── workflows/
│   ├── 01-discovery-to-backlog.md         ← From meeting notes or brief → epics + stories
│   ├── 02-prototype-to-stories.md         ← From Figma or wireframe → stories
│   ├── 03-codebase-to-backlog.md          ← From existing codebase → gap-based backlog
│   ├── 04-story-evaluation.md             ← Quality review before sprint
│   └── 05-dev-ready-handoff.md           ← Zero-ambiguity handoff to engineering
│
├── prompts/                               ← Use directly if not running the agent
│   ├── generate-epics.md
│   ├── generate-user-stories.md
│   ├── generate-acceptance-criteria.md
│   ├── generate-bdd-scenarios.md
│   ├── refine-stories.md
│   ├── split-stories.md
│   ├── identify-edge-cases.md
│   └── evaluate-story-quality.md
│
├── examples/
│   ├── sample-product-brief.md            ← Full product brief (RetailOps)
│   ├── sample-epics.md                    ← RICE-scored epics from the sample brief
│   ├── sample-user-stories.md             ← Two complete stories in canonical format
│   ├── sample-evaluation.md               ← Before-refinement evaluation with scores
│   └── before-and-after-story.md          ← Full story transformation with ROI analysis
│
└── templates/                             ← Canonical formats for every artifact
    ├── epic-template.md                   ← 10-section epic format
    ├── user-story-template.md             ← Full story format with all required sections
    ├── acceptance-criteria-template.md    ← AC checklist format + BDD distinction guide
    └── evaluation-template.md             ← Batch story evaluation scorecard
```

---

## The Workflow (When Using Prompts Directly)

If you prefer to use prompts individually rather than the agent:

| Step | What | Prompt |
|---|---|---|
| 1 | Validate assumptions before writing | [fast-feedback-loop.md](docs/fast-feedback-loop.md) |
| 2 | Generate epics | [generate-epics.md](prompts/generate-epics.md) |
| 3 | Generate user stories | [generate-user-stories.md](prompts/generate-user-stories.md) |
| 4 | Generate AC + BDD together | [generate-acceptance-criteria.md](prompts/generate-acceptance-criteria.md) |
| 4b | Generate BDD from existing AC | [generate-bdd-scenarios.md](prompts/generate-bdd-scenarios.md) |
| 5 | Evaluate story quality | [evaluate-story-quality.md](prompts/evaluate-story-quality.md) |
| 5b | Fix low-quality stories | [refine-stories.md](prompts/refine-stories.md) |
| 5c | Split oversized stories | [split-stories.md](prompts/split-stories.md) |
| 6 | Dev-ready handoff check | [05-dev-ready-handoff.md](workflows/05-dev-ready-handoff.md) |

---

## Story Quality Bar

Every story must pass 7 dimensions before entering a sprint (max score: 21):

| Dimension | What it checks |
|---|---|
| User Clarity | Specific persona — role + context, not "user" |
| Business Value | Measurable "so that" clause |
| AC Verifiable | Declarative checklist, no Given/When/Then |
| BDD Testable | All four scenario types present and correctly formed |
| Story Size | Completable in 1–3 days |
| Edge Cases | Covered across AC and scenarios |
| Dependency Clarity | All blockers explicitly named |

**19–21:** Dev-ready · **15–18:** Needs refinement · **Below 15:** Rework required

---

## Core Principles

1. **Load the agent once, work conversationally** — no prompt copy-pasting required
2. **Validate before you write** — confirm direction before investing in story writing
3. **Stories describe what and why — never how** — no Implementation Notes, Technical Design, or Pseudocode
4. **AC is a checklist; BDD is the proof** — they are complementary, not interchangeable
5. **Dev-ready means no surprises** — an engineer can start without a follow-up meeting
6. **The codebase is a product artifact** — existing code reveals constraints others miss
7. **Evaluation is part of the process** — every story passes a quality gate before sprint

---

## Contributing

Contributions welcome. Open a PR with new prompts, workflow variations, integration examples, or agent improvements. Open an issue to discuss before submitting large changes.

---

## License

MIT — use freely, attribute if you share.

---

## About This Repository

This repository documents experiments and approaches I use when working with AI-assisted product workflows. This is my personal framework to build a backlog.

AI is a tool to accelerate product thinking, not replace it.
Human judgment remains critical.

— Yanet Morales Ramirez [https://www.linkedin.com/in/yanetmoralesr/]

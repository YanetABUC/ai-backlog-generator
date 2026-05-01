# AI Backlog Generator

> A practical framework for generating, evaluating, and refining AI-assisted product backlogs — from discovery to dev-ready items, with built-in Jira integration.

*By Yanet Morales Ramirez*

---

## AI-Native Backlog Generation

This repository documents how I approach backlog creation using AI — from early discovery and prototyping through to Jira-ready items. Instead of writing backlog items in isolation, it combines:

- **Product discovery** — extract structure from raw notes, briefs, and research
- **Fast feedback loops** — validate assumptions before investing in item writing
- **Codebase analysis** — surface constraints, gaps, and existing behavior from the code itself
- **AI-assisted generation** — produce epics, user stories, bugs, spikes, tasks, acceptance criteria, and BDD scenarios
- **Structured evaluation** — score every item against a quality bar before it enters a sprint
- **Jira integration** — push dev-ready items directly from the local pipeline, with conflict detection on updates

The goal is not just to generate backlog items, but to create high-quality, context-aware work that engineering teams can act on immediately — without a follow-up meeting.

---

## Why This Matters

AI can generate user stories easily. But most AI-generated backlogs lack context, ignore technical constraints, miss edge cases, and create more confusion than clarity.

This framework solves that by combining AI generation, human validation, engineering context, and structured evaluation — producing items that are actually usable.

---

## Who This Is For

- Product Managers adopting AI-native workflows
- Business Analysts bridging discovery and delivery
- Engineering teams wanting dev-ready items without constant back-and-forth
- Teams using AI-driven agile frameworks

---

## Getting Started

### Option A: Claude Code Skills (recommended for teams)

Claude Code skills let you run the full pipeline from the terminal — generate items, evaluate them, refine them, and push to Jira — all by referencing items by ID. No copy-pasting required.

1. Install [Claude Code](https://claude.ai/code)
2. Open this repository
3. The skills are in `.claude/commands/backlog/` — invoke them with `/backlog:{skill-name}`

**First-time setup for Jira:**
```
/backlog:jira-config
```
Then set your API token as an environment variable (never stored on disk):
```bash
export JIRA_API_TOKEN=your_token_here
```

See [Claude Code Skills](docs/claude-code-skills.md) for the full skill reference.

### Option B: Claude Projects (conversational agent)

1. Open [claude.ai](https://claude.ai) → Create a new Project
2. Click **Set project instructions** → paste the contents of [`agent.md`](agent.md)
3. Add these files to **Project Knowledge** (optional but recommended):
   - [`templates/user-story-template.md`](templates/user-story-template.md)
   - [`templates/epic-template.md`](templates/epic-template.md)
   - [`docs/backlog-quality-criteria.md`](docs/backlog-quality-criteria.md)
4. Start a conversation

### Option C: Custom GPT (ChatGPT)

1. Explore GPTs → Create → paste [`agent.md`](agent.md) into Instructions
2. Upload the template files to Knowledge

### Option D: Cursor / VS Code

Copy the system prompt from [`agent.md`](agent.md) into `.cursorrules` in your project root.

### Option E: Use Prompts Directly

All prompts are available individually in the [`prompts/`](prompts/) folder. Copy the relevant prompt into any AI chat session.

---

## How It Works — Claude Code Skills in Action

```
/backlog:generate-epics
```
> Claude asks about the initiative, generates 3–5 epics, saves each to `backlog/epics/draft/EP-001-*.md`, and registers them in the counter.

```
/backlog:generate-items EP-001
```
> Generates 5–8 User Stories for EP-001, saves each to `backlog/backlog-items/draft/US-002-*.md`.

```
/backlog:evaluate-item US-002
```
> Reads the file, scores it on 8 dimensions, saves the report to `backlog/reports/US-002-eval-*.md`, moves the file to `in-review/`. No paste required.

```
/backlog:refine-item US-002
```
> Reads the file and the evaluation report, rewrites the story in place.

```
/backlog:dev-ready-handoff US-002
```
> Runs the pre-sprint checklist, detects gaps, generates a kickoff note, moves the file to `ready/`.

```
/backlog:jira-push EP-001
```
> Creates the Jira Epic, then creates and links all `ready` child items (US-002, BUG-005, etc.) in one command.

---

## How It Works — Conversational Examples

Once the agent is loaded (Option B or C), the experience is conversational:

**Starting a new backlog:**
> **You:** I want to build a vendor replenishment automation feature. Warehouse managers spend 2–3 hours a day on manual stock reviews.
>
> **Agent:** Got it. Paste your brief and I'll extract the problem statement, validate the key assumptions, and generate epics. Want a quick feedback loop first to confirm direction, or jump straight to epics?

**Generating epics:**
> **You:** Generate epics from this brief. [paste brief]
>
> **Agent:** [generates 4 epics in the canonical 10-section format] Want me to RICE-score these before writing stories?

**Evaluating quality:**
> **You:** Evaluate these stories. [paste]
>
> **Agent:** [scores each story on 8 dimensions, flags specific issues, provides targeted fixes] Stories 1, 3, and 4 are dev-ready. Stories 2 and 5 need targeted fixes — want me to rewrite them now?

**Splitting a large story:**
> **You:** This story is too large. Split it. [paste]
>
> **Agent:** [splits into 3 sprint-sized stories with delivery order and full template sections per story]

---

## Backlog Item Types

This framework supports four types of backlog items, each with its own format and quality rubric:

```
Epic (EP)
 └── User Story (US)
      ├── Acceptance Criteria   ← declarative checklist: conditions for "done"
      └── BDD Scenarios         ← Gherkin: proof the conditions are met
 └── Bug (BUG)
 └── Spike (SPK)
 └── Task (TSK)
```

**Acceptance Criteria ≠ BDD Scenarios.**

| | Acceptance Criteria | BDD Scenarios |
|---|---|---|
| Format | Declarative numbered list | Gherkin — Given/When/Then |
| Purpose | Define conditions for "done" | Prove those conditions in specific situations |
| Coverage | What must be true | Happy Path + Validation + Edge Case + Failure Handling |

---

## Repository Structure

```
ai-backlog-generator/
│
├── agent.md                               ← Load into Claude, ChatGPT, or Cursor
├── README.md                              ← You are here
│
├── .claude/
│   └── commands/
│       └── backlog/                       ← 16 Claude Code skills
│           ├── generate-epics.md
│           ├── generate-items.md          ← US · BUG · SPK · TSK
│           ├── generate-ac.md
│           ├── generate-bdd.md
│           ├── evaluate-item.md
│           ├── refine-item.md
│           ├── split-item.md
│           ├── identify-edge-cases.md
│           ├── discovery-to-backlog.md
│           ├── codebase-to-backlog.md
│           ├── dev-ready-handoff.md
│           ├── audit-items.md
│           ├── sprint-prep.md
│           ├── backlog-agent.md
│           ├── jira-config.md
│           └── jira-push.md
│
├── backlog/                               ← Generated items live here (gitignored content)
│   ├── counter.json                       ← Global ID registry
│   ├── .config.template.json             ← Jira config template
│   ├── epics/
│   │   ├── draft/
│   │   └── ready/
│   ├── backlog-items/
│   │   ├── draft/
│   │   ├── in-review/
│   │   ├── refined/
│   │   ├── ready/
│   │   └── uploaded/
│   └── reports/                           ← Evaluation and handoff reports
│
├── docs/
│   ├── overview.md                        ← How the full framework fits together
│   ├── claude-code-skills.md             ← Full Claude Code skill reference
│   ├── backlog-quality-criteria.md        ← 8-dimension quality rubric
│   ├── prompt-evaluation-framework.md     ← How to evaluate and improve AI output
│   ├── fast-feedback-loop.md              ← Validate assumptions before writing items
│   └── handoff-from-codebase.md          ← Generate backlog from existing code
│
├── workflows/
│   ├── 01-discovery-to-backlog.md         ← From notes or brief → epics + items
│   ├── 02-prototype-to-stories.md         ← From Figma or wireframe → stories
│   ├── 03-codebase-to-backlog.md          ← From existing codebase → gap-based backlog
│   ├── 04-story-evaluation.md             ← Quality review before sprint
│   └── 05-dev-ready-handoff.md           ← Zero-ambiguity handoff to engineering
│
├── prompts/                               ← Use directly without Claude Code
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
│   ├── sample-product-brief.md
│   ├── sample-epics.md
│   ├── sample-user-stories.md
│   ├── sample-evaluation.md
│   └── before-and-after-story.md
│
└── templates/
    ├── epic-template.md
    ├── user-story-template.md
    ├── acceptance-criteria-template.md
    └── evaluation-template.md
```

---

## The Workflow (Without Claude Code)

| Step | What | Resource |
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

## User Story Quality Bar

User Stories are scored on 8 dimensions (1–3 each, max 24). Grade = (total / 24) × 10.

| Dimension | What it checks |
|---|---|
| User Clarity | Specific persona — role + context, not "user" |
| Business Value | Measurable "so that" clause |
| AC Quality | Declarative checklist, no Given/When/Then |
| BDD Scenarios | All four types present and correctly formed |
| Story Size | Completable in 1–3 days |
| Edge Case Coverage | Happy path + error state + 2+ explicit edge cases |
| Dependency Clarity | All blockers explicitly named |
| Conciseness / NFRs / Out of Scope | No FR sections, no shape prescription, measurable NFR thresholds |

**≥ 9.0:** Dev-ready · **7.0–8.9:** Needs refinement · **< 7.0:** Requires rework

---

## Core Principles

1. **Load the agent once, work conversationally** — no prompt copy-pasting required
2. **Validate before you write** — confirm direction before investing in item writing
3. **Items describe what and why — never how** — no Implementation Notes, Technical Design, or Pseudocode
4. **AC is a checklist; BDD is the proof** — they are complementary, not interchangeable
5. **Dev-ready means no surprises** — an engineer can start without a follow-up meeting
6. **The codebase is a product artifact** — existing code reveals constraints others miss
7. **Evaluation is part of the process** — every item passes a quality gate before sprint
8. **Files are the source of truth** — items are saved locally and pushed to Jira, not the other way around

---

## Contributing

Contributions welcome. Open a PR with new prompts, workflow variations, integration examples, or agent improvements. Open an issue to discuss before submitting large changes.

---

## License

MIT — use freely, attribute if you share.

---

## About This Repository

This repository documents experiments and approaches I use when working with AI-assisted product workflows. This is my personal framework to build a backlog.

AI is a tool to accelerate product thinking, not replace it. Human judgment remains critical.

— Yanet Morales Ramirez [https://www.linkedin.com/in/yanetmoralesr/]

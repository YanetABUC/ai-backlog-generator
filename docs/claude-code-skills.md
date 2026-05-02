# Claude Code Skills for Backlog Generation

This framework ships with 16 Claude Code skills that turn prompts, templates, and quality standards into one-line commands. Skills read and write files automatically — you reference items by ID or Jira link, never by copy-pasting content.

---

## How It Works

### File-Based Pipeline

Every epic and backlog item is saved as a markdown file with a globally unique ID. Items move through status folders as they progress through the pipeline.

```
backlog/
├── discovery/        ← problem statements, domain models, gap analyses, plans
├── epics/
│   ├── draft/        ← generate-epics writes here
│   └── ready/        ← dev-ready-handoff moves epics here
├── backlog-items/
│   ├── draft/        ← generate-items writes here
│   ├── in-review/    ← evaluate-item moves items here
│   ├── refined/      ← (manual staging if needed)
│   ├── ready/        ← dev-ready-handoff moves items here
│   └── uploaded/     ← jira-push moves items here after sync
├── reports/          ← evaluation scorecards and handoff reports
└── DoD.md            ← team Definition of Done (optional — used by generation, evaluation, refinement, and handoff skills)
```

### Global ID Counter

All items share one sequential counter regardless of type. IDs encode the item type:

| Prefix | Type | Example |
|---|---|---|
| EP | Epic | EP-001 |
| US | User Story | US-002 |
| BUG | Bug | BUG-003 |
| SPK | Spike | SPK-004 |
| TSK | Task | TSK-005 |

`backlog/counter.json` tracks the last assigned number and a full index of every item's path, status, type, and Jira key. Skills read and update this file automatically.

### Reference by ID or Jira Link

Once an item is saved, you never paste its content again. Reference it by ID:

```
/backlog:evaluate-item US-003
/backlog:dev-ready-handoff EP-001
/backlog:jira-push US-005
```

Or by Jira link after the first push:

```
/backlog:evaluate-item https://company.atlassian.net/browse/PROJ-42
```

### Report Linking

Evaluation and handoff reports are saved locally in `backlog/reports/`. The path is written into the item's frontmatter — it never goes to Jira.

```yaml
reports:
  evaluation: backlog/reports/US-003-eval-2026-05-01.md
  handoff: backlog/reports/US-003-handoff-2026-05-01.md
```

---

## Setup

### Install Claude Code

Download from [claude.ai/code](https://claude.ai/code) and open this repository.

The skills are in `.claude/commands/backlog/` — no configuration needed to start generating and evaluating items locally.

### Enable Jira Integration (optional)

Run `/backlog:jira-config` and follow the prompts. You will need:
- Your Jira base URL (`https://yourcompany.atlassian.net`)
- Your project key (`PROJ`)
- Your Jira email
- A Jira API token — create one at [id.atlassian.com/manage-profile/security/api-tokens](https://id.atlassian.com/manage-profile/security/api-tokens)

Set the token as an environment variable (never stored on disk):
```bash
export JIRA_API_TOKEN=your_token_here
```

The skill writes `backlog/.config.json`, which is gitignored.

### Using This in Another Project

Copy the `.claude/commands/backlog/` folder into your project root. Also copy the `backlog/` folder structure (or let the skills create it on first use). The skills will be available in any Claude Code session in that directory.

---

## The 16 Skills

---

### Tier 1: Atomic Skills

Each handles a single focused task.

---

#### `/backlog:generate-epics`

Generates product epics from a brief, saves each to `backlog/epics/draft/`, and registers them in the counter.

**When to use:** You have a validated problem statement or brief and need to structure work into shippable increments.

**Inline:**
```
/backlog:generate-epics We're building a vendor contract module for procurement managers. Goal: reduce missed renewals from 12/year to 0. Out of scope: e-signatures.
```

**Conversational:**
```
/backlog:generate-epics
```

**Output:** 3–5 epics saved as `EP-{N}-{slug}.md` with 10-section canonical format + optional RICE ranking. After saving, the skill extracts every `[To validate]` item from Section 8 and asks the user to resolve each before story generation is offered.

---

#### `/backlog:generate-items`

Generates backlog items (User Stories, Bugs, Spikes, or Tasks), saves each to `backlog/backlog-items/draft/`, and registers them in the counter.

**When to use:** An epic is defined and needs to be broken into sprint-sized work items.

**Inline:**
```
/backlog:generate-items [paste epic or feature description]
```

**Conversational:**
```
/backlog:generate-items
```
Claude asks for the item type, epic ID, and type-specific context.

**Item types and their formats:**
- **US (User Story):** In order to / As a / I want to / So that + Context + AC + optional BDD + NFRs + Out of Scope
- **BUG:** Summary + Steps to Reproduce + Expected vs. Actual + Severity + Fix AC + Out of Scope
- **SPK (Spike):** Investigation Question + Context + Timebox + Expected Output + Definition of Done
- **TSK (Task):** Description + AC + Dependencies + Out of Scope

**Pre-check:** If an epic ID is referenced, the skill reads the epic and checks for any unresolved `[To validate]` assumptions. If any exist, it surfaces them as questions and waits for answers before generating. If `backlog/DoD.md` exists, it reads the Required AC Coverage and Required NFR Standards sections and uses them to shape the generated content.

**Output:** Files saved as `{PREFIX}-{N}-{slug}.md` with YAML frontmatter.

---

#### `/backlog:generate-ac`

Generates Acceptance Criteria and BDD Scenarios for an existing item and updates the file in place.

**When to use:** An item was generated without quality sections, or the existing AC are vague or incomplete.

**By ID:**
```
/backlog:generate-ac US-003
```

**By Jira link:**
```
/backlog:generate-ac https://company.atlassian.net/browse/PROJ-12
```

**Conversational:**
```
/backlog:generate-ac
```

**Output:** 5–8 declarative AC items + 4 BDD scenarios (Happy Path, Validation, Edge Case, Failure Handling) with AC traceability. Written directly into the item file.

---

#### `/backlog:generate-bdd`

Generates the four required BDD scenarios when AC already exist, and updates the item file.

**By ID:**
```
/backlog:generate-bdd US-003
```

**Output:** 4 Gherkin scenarios with AC traceability comments and a coverage check. Written into the item file.

---

#### `/backlog:evaluate-item`

Scores a backlog item against the appropriate quality rubric, saves the evaluation report, links it in the item file, and moves the item to `in-review/`.

**By ID:**
```
/backlog:evaluate-item US-003
```

**By Jira link:**
```
/backlog:evaluate-item https://company.atlassian.net/browse/PROJ-12
```

**Rubrics by type:**
- **User Story:** 8-dimension rubric (grade /10). Dev-Ready ≥ 9.0, Needs Refinement 7.0–8.9, Requires Rework < 7.0
- **Bug:** 6 pass/fail gates (Steps, Expected/Actual, Severity, Fix AC, Out of Scope)
- **Spike:** 4 pass/fail gates (Question, Timebox, Output, DoD)
- **Task:** 3 pass/fail gates

**Output:** Scorecard per dimension + grade + top 2 specific fixes. If `backlog/DoD.md` exists, a DoD compliance check runs after the rubric — gaps are listed separately and do not change the grade, but must be resolved before handoff. Report saved to `backlog/reports/`. Item moved to `in-review/`.

---

#### `/backlog:refine-item`

Rewrites an item to fix evaluation issues, updates the file in place. Capped at two passes — repeated failure is a context problem, not a wording problem.

**By ID (evaluation report auto-loaded from frontmatter):**
```
/backlog:refine-item US-003
```

**With inline feedback:**
```
/backlog:refine-item US-003 The persona is too generic and the "so that" clause is not measurable
```

If `backlog/DoD.md` exists, the refinement ensures the rewritten story satisfies Required AC Coverage and Required NFR Standards before finishing.

**Output:** Rewritten item file updated in place with fresh `updated_at` timestamp.

---

#### `/backlog:split-item`

Breaks an oversized User Story into 2–4 sprint-sized items, saves each as a new file, and marks delivery order. Splits by user action or workflow step — never by technical layer.

**By ID:**
```
/backlog:split-item US-007
```

**Output:** 2–4 new item files in `draft/` with sequential IDs, dependency annotations, and a scope check confirming no original AC were dropped.

---

#### `/backlog:identify-edge-cases`

Surfaces edge cases across six categories (data, permissions, state, system failures, volume, browser/device) and optionally adds Critical and High cases to the item's AC.

**By ID:**
```
/backlog:identify-edge-cases US-003
```

**Output:** Edge cases with scenario, expected behavior, AC criterion, and severity. Optionally written into the item file.

---

### Tier 2: Workflow Skills

End-to-end guidance through multiple stages.

---

#### `/backlog:discovery-to-backlog`

Guides a PM or BA from raw discovery notes all the way to saved epics and backlog items with IDs assigned.

**Stages:** Discovery Questions → Problem Framing + Save Discovery Record → Epic Generation + Save → Resolve `[To validate]` Assumptions → Item Generation + Save → Quality Gate → Update Discovery Record with Plan

**Inline (skip discovery questions):**
```
/backlog:discovery-to-backlog [paste product brief or discovery notes]
```

**Conversational:**
```
/backlog:discovery-to-backlog
```

**Output:** Discovery record saved to `backlog/discovery/`. Saved epics in `epics/draft/`. Saved items in `backlog-items/draft/`. Quality gate report. Discovery record updated with epic IDs, work order, and what was descoped.

---

#### `/backlog:codebase-to-backlog`

Analyzes an existing product description, identifies capability gaps, and generates saved epics and items for what's missing.

**Inline:**
```
/backlog:codebase-to-backlog [paste product description or gap summary]
```

**Conversational:**
```
/backlog:codebase-to-backlog
```

**Stages:** Domain Model Extraction + Save Discovery Record → Gap Analysis + Update Record → Epic Generation + Save → Resolve `[To validate]` Assumptions → Item Generation + Save → Quality Gate → Update Discovery Record with Plan

**Output:** Discovery record saved to `backlog/discovery/` (domain model, gap analysis with priorities, broken processes, suggested plan). Saved epics in `epics/draft/`. Saved items in `backlog-items/draft/`.

---

#### `/backlog:dev-ready-handoff`

Final pre-sprint review. Runs the dev-ready checklist, detects gaps, writes a Technical Context block, resolves open questions, and generates a sprint kickoff note. Moves the item to `ready/` and links the handoff report in the file.

**By ID:**
```
/backlog:dev-ready-handoff US-003
```

**Multiple IDs:**
```
/backlog:dev-ready-handoff US-001 US-002 US-003
```

The Delivery Gates checklist is read from `backlog/DoD.md` if it exists, using the team's configured standards (unit tests, QA sign-off, analytics events, feature flags). Falls back to built-in defaults if the file is absent.

**Output:** Pre-handoff checklist results + DoD delivery gates + gap list + Technical Context block + open questions + kickoff note. Report saved to `backlog/reports/`. Item moved to `backlog-items/ready/`.

Once in `ready/`, run `/backlog:jira-push {ID}` to create the ticket in Jira.

---

### Tier 3: Composite Skills

Sprint-level decisions across multiple items.

---

#### `/backlog:audit-items`

Rapid quality audit across a backlog set. Reads items by ID list, folder scope, or all items. Classifies each as Ready / Needs Work / Requires Rework and gives a prioritized fix order.

**By ID list:**
```
/backlog:audit-items US-001 US-002 BUG-003 US-004
```

**By folder:**
```
/backlog:audit-items draft
```

**All items:**
```
/backlog:audit-items
```

**Output:** Audit summary table → critical failures with fastest fix → targeted fixes for borderline items → ready list → top 3 systemic issues → prioritized fix order.

---

#### `/backlog:sprint-prep`

Combined sprint preparation: evaluates all items in a set, classifies by readiness, runs dev-ready checks on passing items, saves reports, moves ready items to `ready/`, and generates the kickoff note.

**By ID list:**
```
/backlog:sprint-prep US-001 US-002 BUG-003
```

**By folder (defaults to `in-review`):**
```
/backlog:sprint-prep
```

**Output:** Ready / Fix / Pull classification → dev-ready checklist for passing items → gap detection → saved reports → sprint kickoff note → sprint health summary.

---

#### `/backlog:backlog-agent`

Conversational assistant. Tell it what you're working on in plain language and it routes you to the right skill, reads and writes files automatically, and maintains context across the session.

```
/backlog:backlog-agent
```

Example prompts:
- "I have a product brief and need epics"
- "Write backlog items for EP-002"
- "Score US-003"
- "This story is too big — help me split it"
- "Sprint planning is tomorrow, get my items ready"
- "Push EP-001 and all its ready items to Jira"
- "Set up our Definition of Done"
- "Validate assumptions before we write stories"

**Discovery context:** When generating or refining stories for an epic, the agent automatically scans `backlog/discovery/` for a matching record (matched by epic ID in the frontmatter) and uses the problem statement, gap analysis, and plan as grounding context. If found, it says so — and never asks you to re-explain what's already saved.

The agent never asks you to copy-paste content that is already saved in a file.

---

### Jira Integration Skills

---

#### `/backlog:jira-config`

Sets up and validates the Jira connection. Run once before using `jira-push`.

```
/backlog:jira-config
```

Prompts for your Jira URL, project key, and email. Reads `JIRA_API_TOKEN` from the environment. Writes `backlog/.config.json` (gitignored) and verifies the connection with a live API call.

---

#### `/backlog:jira-push`

Creates or updates a Jira issue from a local item file. Detects conflicts before overwriting. When pushing an Epic, automatically creates and links all `ready` child items.

**Create (item has no Jira key yet):**
```
/backlog:jira-push US-003
```

**Update (item already has a Jira key):**
```
/backlog:jira-push US-003
```
The skill detects the existing key and checks for conflicts: if someone edited the Jira ticket after the last local sync, it shows a diff and asks before overwriting.

**Push an epic with all its ready children:**
```
/backlog:jira-push EP-001
```
Creates the Jira Epic, then creates and links all backlog items where `epic: EP-001` and `status: ready`. Items without a Jira key are created; items with one are updated.

**By Jira link (update only):**
```
/backlog:jira-push https://company.atlassian.net/browse/PROJ-42
```

**After a successful push:** `jira_key` and `jira_synced_at` are written into the item's frontmatter. The file moves to `uploaded/`.

---

## Recommended Workflows

### Starting a new initiative
```
/backlog:discovery-to-backlog
  → saves discovery record to backlog/discovery/
  → resolves [To validate] assumptions before items
→ /backlog:evaluate-item {ID}    (for each generated item)
→ /backlog:refine-item {ID}      (for items below 9.0)
→ /backlog:dev-ready-handoff {ID}
→ /backlog:jira-push {EPIC_ID}   (pushes epic + all ready children)
```

### Adding to an existing product
```
/backlog:codebase-to-backlog
  → saves domain model, gap analysis, and plan to backlog/discovery/
  → resolves [To validate] assumptions before items
→ /backlog:evaluate-item {ID}
→ /backlog:dev-ready-handoff {ID}
→ /backlog:jira-push {EPIC_ID}
```

### Setting up team standards (do once per project)
```
/backlog:backlog-agent → "Set up our Definition of Done"
  → configures backlog/DoD.md with Required AC Coverage, NFR Standards, Delivery Gates
  → used automatically by generate-items, evaluate-item, refine-item, and dev-ready-handoff
```

### Pre-sprint quality check
```
/backlog:sprint-prep             (evaluates in-review items, moves ready ones)
→ /backlog:refine-item {ID}      (for ⚠️ items)
→ /backlog:split-item {ID}       (for oversized items)
→ /backlog:jira-push {EPIC_ID}
```

### Inheriting a backlog
```
/backlog:audit-items             (scans all items)
→ /backlog:refine-item {ID}      (for ⚠️ items)
→ /backlog:generate-items        (for ❌ items — rewrite with better context)
→ /backlog:sprint-prep
```

---

## Quick Reference

| Command | Input | File behavior | Output |
|---|---|---|---|
| `/backlog:generate-epics` | Brief or conversational | Writes to `epics/draft/` | Saved epics with IDs |
| `/backlog:generate-items` | Type + context | Writes to `backlog-items/draft/` | Saved items with IDs |
| `/backlog:generate-ac` | ID, Jira link, or paste | Updates item file in place | AC + BDD sections |
| `/backlog:generate-bdd` | ID, Jira link, or paste | Updates item file in place | 4 BDD scenarios |
| `/backlog:evaluate-item` | ID or Jira link | Writes report, moves to `in-review/` | Scorecard + top 2 fixes |
| `/backlog:refine-item` | ID (+ optional feedback) | Updates item file in place | Rewritten item |
| `/backlog:split-item` | ID | Writes new files | 2–4 split items with IDs |
| `/backlog:identify-edge-cases` | ID or Jira link | Optionally updates AC | Edge cases by category |
| `/backlog:discovery-to-backlog` | Brief or conversational | Writes discovery record + epics + items | Full saved backlog + discovery record |
| `/backlog:codebase-to-backlog` | Product description | Writes discovery record + epics + items | Gap-based saved backlog + discovery record |
| `/backlog:dev-ready-handoff` | ID or Jira link | Writes report, moves to `ready/` | Checklist + kickoff note |
| `/backlog:audit-items` | IDs, folder, or all | Read-only | Readiness classification |
| `/backlog:sprint-prep` | IDs or folder | Writes reports, moves to `ready/` | Ready/Fix/Pull + kickoff |
| `/backlog:backlog-agent` | Plain language | Delegates to appropriate skill | Conversational routing |
| `/backlog:jira-config` | Conversational | Writes `.config.json` | Verified connection |
| `/backlog:jira-push` | ID or Jira link | Updates frontmatter, moves to `uploaded/` | Jira issue created/updated |

---

## Quality Standards

Every skill enforces the same bar:

- User Stories must grade ≥ 9.0/10 on the 8-dimension rubric to be dev-ready
- All four BDD scenario types required: Happy Path, Validation, Edge Case, Failure Handling
- No Functional Requirements sections — observable behaviors belong in AC
- No implementation details: no file paths, API specs, pseudocode, or return type enumerations
- Personas must be role + context — never "user," "admin," or "customer" alone
- Error messages in scenarios must be exact quoted text
- Boundary conditions must state whether thresholds are inclusive or exclusive

See [backlog-quality-criteria.md](backlog-quality-criteria.md) for the full rubric and scoring guide.

# Changelog

All notable changes to this project will be documented in this file.

---

## [v1.1.0] — 2026-05-01

### Claude Code skills (16 skills)

Full file-based pipeline via Claude Code. All skills read and write files automatically — items are referenced by ID, never copy-pasted.

**Tier 1 — Atomic skills (single-task):**
- `/backlog:generate-epics` — generates 3–5 epics in 10-section canonical format, saves to `backlog/epics/draft/`, registers in counter
- `/backlog:generate-items` — generates US, BUG, SPK, or TSK, saves to `backlog/backlog-items/draft/`, registers in counter
- `/backlog:generate-ac` — generates Acceptance Criteria and BDD Scenarios for an existing item, updates file in place
- `/backlog:generate-bdd` — generates the four required BDD scenario types for an item with existing AC, updates file in place
- `/backlog:evaluate-item` — scores items by type (US: 8-dimension rubric /10; BUG: 6 gates; SPK: 4 gates; TSK: 3 gates), saves report to `backlog/reports/`, moves item to `in-review/`
- `/backlog:refine-item` — rewrites item to fix evaluation issues, updates in place, capped at two passes
- `/backlog:split-item` — breaks an oversized story into 2–4 sprint-sized items by user action, never by technical layer
- `/backlog:identify-edge-cases` — surfaces edge cases across six categories, optionally adds Critical and High cases to AC

**Tier 2 — Workflow skills (multi-stage):**
- `/backlog:discovery-to-backlog` — full pipeline from discovery questions to saved epics and items
- `/backlog:codebase-to-backlog` — domain model extraction, gap analysis, epic and item generation for an existing product
- `/backlog:dev-ready-handoff` — pre-sprint checklist, gap detection, Technical Context block, kickoff note, moves item to `ready/`

**Tier 3 — Composite skills (sprint-level):**
- `/backlog:audit-items` — rapid 14-gate pass/fail across a full backlog set with prioritized fix order
- `/backlog:sprint-prep` — combined evaluation + handoff across a sprint set with readiness classification
- `/backlog:backlog-agent` — conversational assistant that routes to the right skill in plain language

**Jira integration:**
- `/backlog:jira-config` — sets up and validates the Jira connection, writes `backlog/.config.json`
- `/backlog:jira-push` — creates or updates Jira issues with conflict detection; pushing an epic creates and links all ready child items

**Infrastructure added:** `backlog/counter.json` (global ID registry), `backlog/.config.template.json` (Jira config template), `.gitignore` for sensitive config files.

---

### Lean canonical story format

User Story template aligned to the lean canonical standard. Removed: separate Business Objective, Problem Context, Desired Outcome sections; Functional Requirements (FR) section; Implementation Notes; shape prescription. These were replaced with a single Context paragraph (2–5 sentences) and numbered declarative AC items.

Affected files: `templates/user-story-template.md`, `prompts/generate-user-stories.md`, `agent.md`, all skills that generate or evaluate stories.

---

### Quality rubric updated to 8 dimensions

User Story evaluation rubric expanded from 7 dimensions (max 21) to 8 dimensions (max 24). Grade formula changed to `round(total / 24 × 10, 1)`, yielding a score out of 10.

New dimension added: **Conciseness / NFRs / Out of Scope** — checks line count, absence of prohibited sections, measurable NFR thresholds, and explicit Out of Scope. Grade thresholds: ≥ 9.0 Dev-Ready, 7.0–8.9 Needs Refinement, < 7.0 Requires Rework.

Affected files: `templates/evaluation-template.md`, `prompts/evaluate-story-quality.md`, `.claude/commands/backlog/evaluate-item.md`, `docs/backlog-quality-criteria.md`, `agent.md`.

---

### Story generation and evaluation refinements

- `generate-items`: User Story rules tightened — no FR sections, no shape prescription, "So that" must be measurable, persona must be role + context
- `evaluate-item`: Rubric dimensions clarified with explicit 1/2/3 scoring criteria per dimension and "Top 2 fixes" requirement in output
- `refine-item`: Two-pass cap enforced with explicit escalation path ("context problem, not a wording problem")
- `audit-items`: 14-gate pass/fail filter with systemic issue detection and root-cause recommendation
- `backlog-agent`: Full routing table, context awareness across session, quality guardrails enforced on every artifact type

---

### Assumption validation gate

Every discovery and generation skill now enforces a mandatory `[To validate]` resolution step before story generation. After epics are saved, all items marked `[To validate]` in Section 8 (Constraints and Assumptions) are surfaced as numbered questions. Story generation is blocked until all answers are recorded and the epic files are updated.

Affected skills: `generate-epics`, `generate-items`, `discovery-to-backlog`, `codebase-to-backlog`, `backlog-agent`. Also applied to `prompts/generate-epics.md`, `prompts/generate-user-stories.md`, `agent.md`, `workflows/01`, `workflows/03`.

---

### Team Definition of Done (`backlog/DoD.md`)

New optional configuration file at `backlog/DoD.md` with three machine-readable sections:
- **Required AC Coverage** — used by `generate-items` and `refine-item` to shape AC
- **Required NFR Standards** — used by `generate-items` and `refine-item` to set measurable thresholds
- **Delivery Gates** — used by `dev-ready-handoff` instead of the hardcoded checklist

`evaluate-item` runs a DoD compliance check after the rubric when the file exists — gaps are reported separately and do not change the grade. Configure via `backlog-agent`: "Set up our Definition of Done."

**Removed:** ambiguous "Definition of Done is documented on each story" checklist items that caused models to add a DoD section inside story files. Explicit "no DoD section" rules added to all story format definitions in `agent.md`, `generate-items.md`, and `prompts/generate-user-stories.md`. Affected checklists: `workflows/01`, `workflows/04`, `docs/backlog-quality-criteria.md`.

---

### Discovery persistence (`backlog/discovery/`)

All discovery artifacts are now saved to `backlog/discovery/` during workflow execution — not just in the chat.

- `discovery-to-backlog`: saves problem statement after Stage 2 confirmation; updates with epic IDs, suggested work order, and descoped items at completion
- `codebase-to-backlog`: saves domain model after Stage 1; updates with prioritized gap analysis (missing capabilities, broken processes, UX gaps) after Stage 2; updates with plan after Stage 5
- `backlog-agent` fast feedback loop: saves assumption analysis to `backlog/discovery/`

Discovery records use consistent YAML frontmatter with a `workflow` field and an `epics` field linking to generated epics.

---

### Agent discovery context

`backlog-agent` now automatically reads discovery records when generating or refining stories. Before generating items for an epic, it scans `backlog/discovery/` for any record whose `epics` frontmatter includes the target epic ID. If found, it reads the full record and uses it as grounding context: problem statement informs story narrative; gap analysis informs scenarios and edge cases; open questions are surfaced before generation; plan confirms scope and descoped items. The agent announces when a record is found and never asks the user to re-explain saved context.

---

## [v1.0.0] — 2026-04-22

### Initial release

**Agent**
- `agent.md` — conversational master agent (ai-backlog-generator) for Claude Projects, Custom GPT, and Cursor

**Templates**
- `epic-template.md` — 10-section canonical epic format (Narrative, Strategic Context, Desired Transformation, Scope, Users & Actors, Functional Expectations, Non-Functional Expectations, Constraints, Dependencies, Success Metrics)
- `user-story-template.md` — full story format (In order to / As a / I want to / So that + Business Objective, Problem Context, Desired Outcome, AC, BDD Scenarios, FRs, NFRs, Out of Scope, Media)
- `acceptance-criteria-template.md` — AC checklist format with AC vs. BDD distinction guide
- `evaluation-template.md` — batch story evaluation scorecard (7 dimensions, max 21)

**Prompts**
- `generate-epics.md`
- `generate-user-stories.md`
- `generate-acceptance-criteria.md`
- `generate-bdd-scenarios.md`
- `refine-stories.md`
- `split-stories.md`
- `identify-edge-cases.md`
- `evaluate-story-quality.md`

**Workflows**
- `01-discovery-to-backlog.md`
- `02-prototype-to-stories.md`
- `03-codebase-to-backlog.md`
- `04-story-evaluation.md`
- `05-dev-ready-handoff.md`

**Docs**
- `overview.md`
- `backlog-quality-criteria.md` — 7-dimension quality bar
- `prompt-evaluation-framework.md`
- `fast-feedback-loop.md`
- `handoff-from-codebase.md`

**Examples**
- `sample-product-brief.md`
- `sample-epics.md`
- `sample-user-stories.md`
- `sample-evaluation.md`
- `before-and-after-story.md`

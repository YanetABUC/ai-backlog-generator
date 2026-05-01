Guide a PM or BA from raw discovery notes or a product brief all the way to saved epics and backlog items, with IDs assigned at each stage.

---

You are acting as a senior product manager and business analyst facilitating the full discovery-to-backlog workflow.

## Handling Input

If the user provided content after the skill command, treat it as the product brief and start at Stage 2.

If no content was provided, start at Stage 1.

---

## Stage 1: Discovery Questions

Tell the user:
> "Let's build your backlog from scratch. Answer as much as you can — we can refine as we go."

Ask:
1. What are we building? (1–3 sentences)
2. Who is it for? (specific roles and context)
3. What problem are we solving? (current struggle and workaround)
4. What is the measurable business goal?
5. What is explicitly out of scope?
6. Why now? (trigger or urgency)
7. What constraints apply? (time, budget, technical, regulatory)
8. What do we know vs. what are we assuming?

---

## Stage 2: Problem Framing

Synthesize the answers into a structured problem statement:

**Problem Statement:**
- **Who:** [specific user — role + context]
- **Struggles with:** [pain point — specific and observable]
- **Current workaround:** [how they cope today]
- **Impact:** [cost of the problem]
- **Solution opportunity:** [capability that would change this]
- **Success looks like:** [measurable outcome]

Present to the user and ask: "Does this capture the problem correctly? Corrections before we move to epics?"

---

## Stage 3: Epic Generation and Save

Once confirmed, generate 3–5 epics following the same process as `generate-epics.md`:

1. Read `backlog/counter.json` for the next available ID
2. Assign IDs sequentially (EP-001, EP-002, ...)
3. Generate each epic in the 10-section canonical format with frontmatter
4. Write each to `backlog/epics/draft/{ID}-{slug}.md`
5. Update `counter.json` for each epic

Tell the user the IDs and paths created.

Ask: "Would you like RICE scores to rank these epics before writing backlog items?"

---

## Stage 4: Backlog Item Generation and Save

Ask: "Which epic should we start with?"

For the selected epic, generate 5–8 backlog items (primarily User Stories, US type) following the same process as `generate-items.md`:

1. Read `backlog/counter.json` for the next available ID
2. Assign IDs sequentially across all types
3. Generate each item with the canonical format and frontmatter, linking `epic` to the parent epic ID
4. Write each to `backlog/backlog-items/draft/{ID}-{slug}.md`
5. Update `counter.json` for each item

Coverage checklist — verify the generated set covers:
- [ ] Primary happy path
- [ ] Alternative user paths
- [ ] Empty state
- [ ] Error states
- [ ] Permission variations
- [ ] Boundary and edge conditions

---

## Stage 5: Quality Gate

Run a rapid quality check (same gates as `audit-items.md`) on all generated items inline. For any item failing a gate, flag it immediately with the specific issue.

Report:
- Items that passed
- Items that need a fix (with the specific gate and a one-line fix)

Tell the user: "Use `/backlog:evaluate-item {ID}` for a full scorecard on any item. When items are ready, run `/backlog:sprint-prep` to finalize for the sprint."

---

## Completion

When all items are created:
> "Your backlog is saved and registered. Next steps:
> 1. `/backlog:evaluate-item {ID}` — score each story
> 2. `/backlog:refine-item {ID}` — fix any below 9.0
> 3. `/backlog:dev-ready-handoff {ID}` — final pre-sprint check
> 4. `/backlog:jira-push {EPIC_ID}` — push the epic and all ready items to Jira"
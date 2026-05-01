Final pre-sprint review. Verifies an engineer can pick up each item and start building without scheduling a meeting. Saves a handoff report and links it in the item file.

---

## Handling Input

The user may provide:
- A local ID (`US-003`, `BUG-005`) — read from file, run handoff
- A Jira link — resolve via `counter.json` `jira_key` lookup, then read
- A list of IDs — run handoff on each in sequence

**If ID or Jira link:**
1. Read `backlog/counter.json` to find the file path
2. Read the item file
3. Run Steps 1–5 below
4. Write report to `backlog/reports/{ID}-handoff-{YYYY-MM-DD}.md`
5. Update item frontmatter: `reports.handoff`, `status` → `ready`, `updated_at`
6. Move file to `backlog/backlog-items/ready/` (or `backlog/epics/ready/` for epics)
7. Update `counter.json`: path and status

If no input was provided, ask: "Provide the item ID or paste the item content."

---

## The Dev-Ready Standard

A story is dev-ready when an engineer can answer all of these from the item alone:
1. Who is this for and why does it matter?
2. What exactly should be built?
3. What does done look like? (Can they test it themselves?)
4. What can go wrong, and how should it be handled?
5. What do they need from other teams before starting?
6. What is out of scope?

---

## Step 1: Pre-Handoff Checklist

Run and report each item:

**Basics:**
- [ ] Title is an action statement, not a noun
- [ ] Persona is specific (role + context, not "user")
- [ ] Business value is explicitly stated
- [ ] Size is S or M (L → use `/backlog:split-item {ID}`)

**Acceptance Criteria:**
- [ ] All criteria are declarative pass/fail — no Given/When/Then in AC
- [ ] Happy path covered (2–3 criteria minimum)
- [ ] Error states covered (API failure, invalid input, permission denied)
- [ ] Empty state addressed if feature displays data
- [ ] No shape prescription anywhere

**Technical context:**
- [ ] Domain entities referenced by business name (not file paths or class names)
- [ ] Performance constraints in NFRs with specific thresholds
- [ ] Security or permission requirements explicit

**Dependencies:**
- [ ] All blockers identified
- [ ] Design assets linked (Figma, prototype URL)
- [ ] API documentation linked if third-party integration
- [ ] All dependencies resolved or have a resolution date

**Definition of Done:**
- [ ] Unit tests expected? (yes/no)
- [ ] Integration tests expected? (yes/no)
- [ ] QA sign-off required? (yes/no)
- [ ] Analytics event to be fired? (yes/no — event name)
- [ ] Feature flag required? (yes/no)

---

## Step 2: Gap Detection

Identify specifically:
1. Any information a developer would need that is NOT in the item
2. Any AC that are ambiguous or untestable as written
3. Any edge cases missing given the feature context
4. Any technical assumptions not validated
5. Any dependencies not called out

Be specific — "The error state for a failed upload is undefined" is useful. "Needs more detail" is not.

Ask the user to resolve every gap before confirming the item is ready.

---

## Step 3: Technical Context Block

Help the user write a Technical Context section to add to the item:

```markdown
## Technical Context

**Relevant entities:** [domain entities and their states]
**Existing behavior to preserve:** [what must not change]
**Known constraints:** [technical limitations]
**Performance considerations:** [if applicable]
**Security notes:** [if applicable]
```

Ask: "Has engineering shared any domain context or constraints I should include here?"

If yes: add the block to the item file.

---

## Step 4: Open Questions

For any unresolved question from Step 2, produce:
- Who can answer it?
- Fastest path to an answer?
- Default assumption the engineer should use if it cannot be resolved before the sprint?

---

## Step 5: Kickoff Note

Generate a ready-to-send note:

```
**[Epic Name] — Sprint Kickoff**

What we're building: [1–2 sentences]
Why it matters: [1 sentence — business goal]
Key constraints: [2–3 bullet points]
Dependency order: [brief sequence]
Contacts: Design ([name]), PM ([name])
[Link to epic in tracker]

Questions: drop them in [Slack channel] or tag me on the item.
```

---

## Step 6: Save Report and Update File

Write `backlog/reports/{ID}-handoff-{YYYY-MM-DD}.md` containing the full checklist results, gap list, open questions, and Technical Context block.

Update the item's frontmatter:
- `reports.handoff`: path to the report
- `status`: `ready`
- `updated_at`: current ISO timestamp

Move file to `backlog/backlog-items/ready/` and update `counter.json`.

Tell the user: "**{ID}** is dev-ready. File moved to `ready/`. Run `/backlog:jira-push {ID}` to create it in Jira."

---

## Red Flags That Mean Not Ready

| Signal | Action |
|---|---|
| "What should happen when X?" | X is a missing edge case — add to AC before handoff |
| "We need to sync before I start" | Clarify now — don't hand off yet |
| "This is bigger than I thought" | Use `/backlog:split-item {ID}` |
| "Design hasn't finalized this" | Block the item — flag dependency |
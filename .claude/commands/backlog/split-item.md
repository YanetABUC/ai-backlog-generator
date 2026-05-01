Break an oversized User Story into 2–4 sprint-sized items, save each as a new file, and update the counter.

---

## Handling Input

The user provides a story ID (e.g. `US-003`) or inline story content.

**If ID provided:**
1. Read `backlog/counter.json` to find the file path
2. Read the item file
3. Verify it is type `Story` — if not, tell the user this skill only applies to User Stories

**If content provided:** Use directly, no file read.

---

## When to Split

- Estimated as Large or Extra-Large
- More than 7–8 Acceptance Criteria
- Engineering estimates more than 3 days
- Story title contains "and"
- Story spans multiple user interactions

---

## Splitting Principles

Never split by technical layer (frontend / backend / database). Always split by:

1. **User action** — one story per distinct thing the user does
2. **User type** — one story per role if behavior differs significantly
3. **Workflow step** — one story per step in a multi-step process
4. **Data state** — one story per meaningful state (empty, populated, error)
5. **Scope boundary** — MVP in story 1, enhancements in story 2+

---

## Splitting Rules

1. Each resulting story delivers standalone user value — no invisible backend-only stories (except technical prerequisites, clearly labeled as TSK)
2. Every resulting story is completable in 1–3 days
3. Each story has its own Acceptance Criteria — no sharing across stories
4. Mark delivery order: which stories block which

---

## Step 1: Generate Split Stories

For each resulting story, use the full canonical User Story format (same as `generate-items.md` for type US), plus:

- **Size:** S / M
- **Depends on:** {ID} / None
- **Blocks:** {ID} / None

If a technical prerequisite is needed, generate a Task (TSK) item instead of a Story, clearly labeled.

---

## Step 2: Assign IDs and Save

For each new item:
1. Read `backlog/counter.json`
2. Assign the next sequential ID
3. Write to `backlog/backlog-items/draft/{ID}-{slug}.md` with proper frontmatter
4. Update `counter.json`

The original story is **not deleted** — mark it in the index with a note. Ask the user: "Should I archive the original {ID} or delete it?"

---

## Step 3: Confirm

Tell the user:
- IDs assigned to each split story
- Delivery order with brief reasoning
- Scope check: confirm all original AC are covered across the new stories, and flag anything dropped

Suggest: "Run `/backlog:evaluate-item {NEW_ID}` on each new story before sprint planning."
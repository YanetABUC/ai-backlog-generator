Generate Acceptance Criteria and BDD Scenarios for a backlog item. Updates the item file in place.

---

You are acting as a senior business analyst and QA lead generating the quality sections for a backlog item.

## The Core Distinction

**Acceptance Criteria** = declarative pass/fail conditions that must be true for the item to be "done." No Given/When/Then.

**BDD Scenarios** = concrete narratives (Gherkin) that prove the criteria are met in specific situations. Four required types: Happy Path, Validation, Edge Case, Failure Handling.

---

## Handling Input

The user may provide:
- A local ID (`US-003`) — read the file and generate AC for it
- A Jira link — resolve to a local file via `counter.json` `jira_key` lookup, then read the file
- Pasted story content — generate AC directly, no file operations

**If ID or Jira link:**
1. Read `backlog/counter.json` to find the file path
2. Read the item file
3. Generate AC and BDD sections based on its content
4. Insert them into the file (replacing empty or placeholder sections)
5. Update `updated_at` in frontmatter
6. Write the updated file back

If no input was provided, ask:
1. Item ID or paste the story narrative
2. What product is this for?
3. What user roles exist?
4. What domain entities are involved? (business terms)
5. What business rules apply?
6. Does the product have a standard error message format?

Then ask: "Do you want both AC + BDD Scenarios, or AC only?"

---

## Acceptance Criteria Requirements

Generate 5–8 items covering:
- [ ] Primary action succeeds (happy path condition)
- [ ] Observable outcome specified (data persisted, status changed, document available)
- [ ] At least one validation failure — what the user sees with exact quoted message
- [ ] At least one system/integration failure — what the user sees
- [ ] Empty state — no data to display
- [ ] Permission behavior — authorized vs. unauthorized
- [ ] At least one boundary condition — state whether threshold is inclusive or exclusive

Format:
```
- AC1: [declarative statement of observable behavior]
- AC2: [declarative statement]
```

No Given/When/Then in AC. No implementation details.

---

## BDD Scenario Requirements

### Happy Path
Primary success flow. Specifies observable results. Proves 1–3 AC items.

### Validation
Invalid input or business rule violation. Exact error message in quotes. States data is preserved.

### Edge Case
Boundary condition. States threshold inclusivity (inclusive or exclusive) explicitly.

### Failure Handling
System or integration failure. Exact recoverable error message in quotes. States no data lost and user can retry.

**Format:**
```gherkin
# Proves AC[N], AC[N]
Scenario: [Descriptive name readable by a non-technical stakeholder]
  Given [precondition]
  When [single action]
  Then [observable result]
  And [secondary result, if needed]
```

After generating, add a traceability summary and flag any AC with no covering scenario.

---

## Rules That Cannot Be Broken

- Error messages must be exact and quoted — never "shows an error"
- Boundary thresholds must state inclusive or exclusive explicitly
- Failure Handling must confirm data preservation and retry capability
- Every AC item must map to at least one scenario — flag any that don't
- One action per When — if two are needed, write two scenarios

---

## After Generating

If operating on a file: tell the user the file was updated in place.

Suggest: "Run `/backlog:evaluate-item {ID}` to confirm the full item reaches grade ≥ 9.0."
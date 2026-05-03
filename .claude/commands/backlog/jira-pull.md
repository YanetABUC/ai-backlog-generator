Import one or more Jira issues into the local pipeline. Accepts a single issue key, a list of keys, a Jira URL, or a sprint name. Detects conflicts with existing local items before writing. Imported items land in draft/ and are queued for quality review.

---

## Prerequisites

- `backlog/.config.json` must exist (run `/backlog:jira-config` first)
- `JIRA_API_TOKEN` environment variable must be set

---

## Handling Input

The user may provide:
- A single issue key: `PROJ-42`
- A Jira issue URL: `https://company.atlassian.net/browse/PROJ-42`
- A list of keys: `PROJ-42 PROJ-43 PROJ-44`
- A sprint name: `sprint:"Sprint 5"` or `sprint:current`
- A JQL query: `jql:"assignee = currentUser() AND status = 'To Do'"`

Read `backlog/.config.json` to get `baseUrl`, `projectKey`, `email`, and `issueTypes`.

---

## Step 1: Fetch from Jira

### Single issue or list of keys

```bash
curl -s \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/issue/{KEY}?fields=summary,description,issuetype,updated,parent,status,priority"
```

Run once per key. Collect all responses before proceeding.

### Sprint pull

For `sprint:current`:
```bash
curl -s \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/search?jql=sprint+in+openSprints()+AND+project={PROJECT_KEY}&fields=summary,description,issuetype,updated,parent,status,priority&maxResults=50"
```

For a named sprint (e.g. `sprint:"Sprint 5"`):
```bash
curl -s \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/search?jql=sprint=%22Sprint+5%22+AND+project={PROJECT_KEY}&fields=summary,description,issuetype,updated,parent,status,priority&maxResults=50"
```

For a JQL query:
```bash
curl -s \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/search?jql={ENCODED_JQL}&fields=summary,description,issuetype,updated,parent,status,priority&maxResults=50"
```

---

## Step 2: Conflict Detection

For each fetched issue, check `backlog/counter.json` for an entry where `jira_key` matches the issue key.

### Case A — Not in counter.json (new import)
No local file exists. Proceed to Step 3 as a fresh import.

### Case B — Exists locally, Jira is newer
`fields.updated` (Jira) > `jira_synced_at` (local frontmatter).

Jira was edited after the last local sync. Show:
```
⚠️ Conflict: {JIRA_KEY} — {local ID} ({local title})

Jira last modified:  {JIRA_UPDATED}
Local last synced:   {JIRA_SYNCED_AT}

Jira summary: {JIRA_SUMMARY}
Local title:  {LOCAL_TITLE}

Overwrite local file with Jira version? (yes / no / show-diff)
```

- **yes**: overwrite local file, update frontmatter, do not change local status
- **no**: skip this item, note it in the summary
- **show-diff**: display Jira description vs. local body side by side, then ask again

### Case C — Exists locally, local is newer or in sync
`jira_synced_at` >= `fields.updated`. Tell the user: "{JIRA_KEY} — already in sync. Skipping."

---

## Step 3: Convert Jira Issue to Local Format

### Determine local item type

Map Jira `issuetype.name` to local type using the reverse of `issueTypes` in `.config.json`:

| Jira type | Local type |
|---|---|
| Epic | EP |
| Story | US |
| Bug | BUG |
| Task | TSK |
| Any unmapped type | TSK (note the original type in the body) |

### Assign local ID

- **New import**: read `backlog/counter.json`, get the next available counter for this type, assign the ID (e.g. `US-007`)
- **Overwrite (Case B)**: reuse the existing local ID

### Convert Jira wiki markup to markdown

| Jira | Markdown |
|---|---|
| `h1. Heading` | `# Heading` |
| `h2. Heading` | `## Heading` |
| `*bold*` | `**bold**` |
| `_italic_` | `*italic*` |
| `{{code}}` | `` `code` `` |
| `{code}...{code}` | ` ```...``` ` |
| `* item` | `- item` |
| `# item` (ordered) | `1. item` |
| `[text\|url]` | `[text](url)` |
| `\n----\n` | `---` |

Strip any Jira-specific macros that have no markdown equivalent.

### Build the local file

```markdown
---
id: {LOCAL_ID}
title: "{JIRA_SUMMARY}"
type: {LOCAL_TYPE}
status: draft
epic: null
jira_key: {JIRA_KEY}
jira_synced_at: {JIRA_UPDATED}
imported_from_jira: true
created_at: {CURRENT_ISO_TIMESTAMP}
updated_at: {CURRENT_ISO_TIMESTAMP}
reports: {}
---

> **Imported from Jira {JIRA_KEY}** — Jira status: {JIRA_STATUS} | Priority: {JIRA_PRIORITY}
> This item was imported and has not been evaluated against the quality rubric. Run `/backlog:full-quality-review {LOCAL_ID}` to assess and reformat.

---

{CONVERTED_DESCRIPTION_BODY}
```

If `fields.description` is empty, write:
```
> **No description in Jira.** Add content and run `/backlog:full-quality-review {LOCAL_ID}`.
```

### Resolve parent epic

If the Jira issue has a `parent` field (next-gen) or `customfield_10014` (classic epic link):
- Look up the parent Jira key in `counter.json`
- If a local epic exists with that `jira_key`, set `epic` in frontmatter to the local epic ID
- If no local epic exists, leave `epic: null` and note it in the import summary

---

## Step 4: Save Files and Update Counter

For each imported item:

1. Write the file to `backlog/backlog-items/draft/{LOCAL_ID}-{slug}.md` (or `backlog/epics/draft/` for epics)
   - Slug: lowercase title, spaces replaced with `-`, max 50 characters
2. Update `backlog/counter.json`:
   - **New import**: add entry with `id`, `type`, `title`, `status: draft`, `path`, `jira_key`, `epic`
   - **Overwrite**: update `path`, `jira_key`, `jira_synced_at`, `updated_at` — do not change `status`

---

## Step 5: Epic Pull — Offer to Pull Child Items

If any imported item is an Epic, ask:
> "Imported EP-{N} ({JIRA_KEY}). Pull all child issues linked to this epic in Jira? (yes / no)"

If yes: run a JQL fetch for `"Epic Link" = {JIRA_KEY} OR parent = {JIRA_KEY}` and run the full import flow for each child.

---

## Step 6: Import Summary

```
## Jira Pull Summary

Imported: [N] items | Skipped (in sync): [N] | Conflicts resolved: [N] | Conflicts skipped: [N]

### Imported
| Local ID | Jira Key | Title | Action |
|---|---|---|---|
| US-007 | PROJ-42 | {title} | New import |
| US-008 | PROJ-43 | {title} | Overwritten (Jira was newer) |

### Skipped
| Jira Key | Reason |
|---|---|
| PROJ-44 | Already in sync |
| PROJ-45 | Conflict — user chose to skip |

### Needs Attention
| Local ID | Issue |
|---|---|
| US-007 | No description in Jira — add content before evaluating |
| US-008 | Parent epic PROJ-38 not found locally — epic link unresolved |
```

**Next step:** "Run `/backlog:full-quality-review {ID}` on each imported item. Imported items are in `draft/` and have not been evaluated."

For multiple items: "Run `/backlog:audit-items draft` for a rapid pass across all imported items before running full reviews."

---

## Error Handling

| HTTP code | Message |
|---|---|
| 401 | "Authentication failed. Verify `JIRA_API_TOKEN` is set and not expired." |
| 403 | "Permission denied. Your Jira account may not have View permission on this project." |
| 404 | "Issue {KEY} not found. Check the key and project." |
| 429 | "Jira rate limit hit. Wait 60 seconds and retry." |
| JQL error | Show Jira's error message and suggest correcting the query syntax. |

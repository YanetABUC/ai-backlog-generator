Create or update a Jira issue from a local backlog item. Detects conflicts before overwriting. When pushing an Epic, automatically creates and links all its ready child items.

---

## Prerequisites

- `backlog/.config.json` must exist (run `/backlog:jira-config` first)
- `JIRA_API_TOKEN` environment variable must be set
- Item must be in `ready` or `uploaded` status in `backlog/counter.json`

---

## Handling Input

The user provides an item ID (`EP-001`, `US-003`) or a Jira link.

**If local ID:** Read `backlog/counter.json`, find the path, read the file.

**If Jira link** (e.g. `https://company.atlassian.net/browse/PROJ-42`): Extract the key `PROJ-42`, search `counter.json` index for matching `jira_key`, read that file.

Also read `backlog/.config.json` to get `baseUrl`, `projectKey`, `email`, and `issueTypes`.

---

## Step 1: Determine Operation (Create or Update)

Check the item's frontmatter:
- `jira_key: null` → **Create** mode
- `jira_key` has a value → **Update** mode

---

## Step 2A: Create Mode

### Convert item to Jira fields

Map frontmatter and content to Jira fields:

| Local field | Jira field |
|---|---|
| `title` | `summary` |
| `type` (mapped via `issueTypes` in config) | `issuetype.name` |
| full markdown content (converted) | `description` |
| `epic` frontmatter (if item is a child) | `parent.key` (next-gen) or `customfield_10014` (classic) |

**Content conversion — markdown to Jira wiki markup:**
- `# Heading` → `h1. Heading`, `## Heading` → `h2. Heading`, etc.
- `**bold**` → `*bold*`
- `*italic*` → `_italic_`
- `` `code` `` → `{{code}}`
- Fenced code blocks → `{code}...{code}`
- `- item` → `* item`

Strip frontmatter block before converting.

### Build the API payload

```json
{
  "fields": {
    "project": { "key": "{PROJECT_KEY}" },
    "summary": "{title}",
    "issuetype": { "name": "{mapped issue type}" },
    "description": "{converted content}"
  }
}
```

For child items with an epic: look up the epic's `jira_key` from `counter.json`. If the epic has no `jira_key` yet, tell the user: "Push the epic first: `/backlog:jira-push {EPIC_ID}`."

For next-gen projects, add: `"parent": { "key": "{EPIC_JIRA_KEY}" }`
For classic projects, add: `"customfield_10014": "{EPIC_JIRA_KEY}"`

### Call the API

```bash
curl -s -X POST \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{PAYLOAD}' \
  "{BASE_URL}/rest/api/2/issue"
```

On success (201): extract `key` from response (e.g. `PROJ-42`).

### Update local file

Update frontmatter:
- `jira_key`: set to returned key (e.g. `PROJ-42`)
- `jira_synced_at`: current ISO timestamp
- `status`: `uploaded`
- `updated_at`: current ISO timestamp

Move file from current folder to `backlog/backlog-items/uploaded/` (or `backlog/epics/uploaded/` for epics).

Update `backlog/counter.json`: change `path`, `status`, and `jira_key` for this ID.

Tell the user: "Created **{JIRA_KEY}** in Jira. Local file moved to `uploaded/`."

---

## Step 2B: Update Mode

### Fetch current Jira state

```bash
curl -s \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/issue/{JIRA_KEY}?fields=updated,summary,description"
```

Extract `fields.updated` (Jira's last-modified timestamp).

### Conflict detection

Compare:
- **Jira `updated`** vs. **local `jira_synced_at`**

If Jira `updated` > local `jira_synced_at`, a conflict exists: someone edited the Jira ticket after the last local sync.

Show the user:
```
⚠️ Conflict detected for {JIRA_KEY}

Jira was last modified: {JIRA_UPDATED}
Local last synced:      {JIRA_SYNCED_AT}

Jira summary:   {JIRA_SUMMARY}
Local title:    {LOCAL_TITLE}

Overwrite Jira with the local version? (yes / no / show-diff)
```

If user says **no**: abort. Tell the user to manually reconcile and re-run.

If user says **show-diff**: display a plain-text comparison of Jira description vs. local content, then ask again.

If user says **yes** or no conflict exists: proceed to update.

### Call the update API

```bash
curl -s -X PUT \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{PAYLOAD}' \
  "{BASE_URL}/rest/api/2/issue/{JIRA_KEY}"
```

Payload contains `summary` and `description` fields only — do not overwrite status or assignee.

On success (204): update `jira_synced_at` in local frontmatter to current timestamp. Update `updated_at`.

Tell the user: "Updated **{JIRA_KEY}** in Jira."

---

## Step 3: Epic Push — Auto-Link Child Items

When the pushed item is an Epic (`type: Epic`), after creating/updating the epic:

1. Read `backlog/counter.json`
2. Find all entries where:
   - `epic` matches this epic's ID
   - `status` is `ready`
   - `jira_key` is `null` (not yet in Jira)
3. For each child item found, run the Create flow automatically
4. Report a summary:

```
Epic {JIRA_KEY} pushed.

Child items created:
✅ {CHILD_JIRA_KEY} — {title} ({ID})
✅ {CHILD_JIRA_KEY} — {title} ({ID})

Child items skipped (not in ready status):
⏭ {ID} — {title} (status: {status})
```

If there are no child items in `ready`, tell the user: "No child items are in `ready` status. Move items through the pipeline first, then re-push the epic to link them."

---

## Error Handling

| HTTP code | Message |
|---|---|
| 400 | "Jira rejected the payload. Check that issue type names in `.config.json` match your project's configured types." |
| 401 | "Authentication failed. Verify `JIRA_API_TOKEN` is set and not expired." |
| 403 | "Permission denied. Your Jira account may not have Create Issue permission on project `{PROJECT_KEY}`." |
| 404 | "Issue or project not found. Verify the project key and base URL in `.config.json`." |
| 429 | "Jira rate limit hit. Wait 60 seconds and retry." |
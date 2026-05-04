# Jira Integration

The framework maintains local markdown files as the source of truth and syncs with Jira in both directions. You push when items are ready, pull when Jira has items that need to enter the pipeline, and the framework handles conflict detection when both sides have changed.

---

## Setup

Run this once before using any Jira skill:

```
/backlog:jira-config
```

The skill will ask for:
- **Jira base URL** — `https://yourcompany.atlassian.net` (no trailing slash)
- **Project key** — the prefix in your issue keys, e.g. `PROJ` from `PROJ-42`
- **Your Jira email**
- **Project type** — Next-gen (team-managed) or Classic (company-managed)
- **Custom issue type names** — only if your project uses non-default names

Configuration is saved to `backlog/.config.json`, which is gitignored and never committed.

### API token

Create a Jira API token at `id.atlassian.com/manage-profile/security/api-tokens` and set it as an environment variable — it is never written to disk:

```bash
export JIRA_API_TOKEN=your_token_here
```

Add this to your shell profile (`~/.zshrc` or `~/.bashrc`) to make it permanent.

---

## Pushing to Jira

```
/backlog:jira-push US-003
/backlog:jira-push EP-001
```

Items must be in `ready` status before pushing. Pushing an Epic automatically creates and links all its `ready` child items in one call.

**If the item already has a `jira_key`**, the skill runs an update instead of a create. Before overwriting, it fetches the current Jira state and compares timestamps. If Jira was edited after the last local sync, it shows a diff and asks before proceeding.

**Reference items by local ID or Jira link:**
```
/backlog:jira-push https://yourcompany.atlassian.net/browse/PROJ-42
```

After a successful push, the item's frontmatter is updated with `jira_key` and `jira_synced_at`, and the file moves to `uploaded/`.

---

## Pulling from Jira

Use `/backlog:jira-pull` when tickets were created or edited directly in Jira — by a developer, a stakeholder, or a PO working outside the framework. Pulling imports them into the local pipeline where they can be evaluated and improved.

### Single issue or list

```
/backlog:jira-pull PROJ-42
/backlog:jira-pull PROJ-42 PROJ-43 PROJ-44
/backlog:jira-pull https://yourcompany.atlassian.net/browse/PROJ-42
```

### Current sprint

```
/backlog:jira-pull sprint:current
```

### Named sprint

```
/backlog:jira-pull sprint:"Sprint 5"
```

### JQL query

```
/backlog:jira-pull jql:"assignee = currentUser() AND status = 'To Do'"
```

Imported items land in `backlog/backlog-items/draft/` with a new local ID assigned. They are flagged as `imported_from_jira: true` in their frontmatter.

**Pulling an Epic** offers to also pull all its child issues in one follow-up call.

---

## Two-Way Sync and Conflict Detection

The framework tracks `jira_synced_at` in every item's frontmatter — the timestamp of the last successful push or pull. Conflicts are detected by comparing this against Jira's own `updated` timestamp.

### On push (local → Jira)

If Jira was edited after the last local sync:

```
⚠️ Conflict: PROJ-42 — US-003

Jira last modified:  2026-05-03T14:22:00Z
Local last synced:   2026-05-01T09:10:00Z

Overwrite Jira with the local version? (yes / no / show-diff)
```

### On pull (Jira → local)

Three outcomes per item:

| Situation | Action |
|---|---|
| Not in local pipeline | Fresh import — new local ID assigned |
| Jira is newer than local | Conflict prompt — ask before overwriting |
| Already in sync | Skip — no changes |

Choose **show-diff** at any conflict prompt to see a side-by-side comparison before deciding.

---

## Item Type Mapping

| Local type | Jira issue type |
|---|---|
| EP | Epic |
| US | Story |
| BUG | Bug |
| SPK | Story |
| TSK | Task |

If your Jira project uses different names (e.g. "User Story" instead of "Story"), set the mapping during `/backlog:jira-config` or edit `backlog/.config.json` directly.

---

## What Happens to Imported Items

Jira tickets are rarely in the framework's canonical format. An imported item gets:
- A local ID (`US-007`, `BUG-012`, etc.)
- Its Jira content converted from wiki markup to markdown
- Status set to `draft`
- An `imported_from_jira: true` flag
- A notice at the top of the file indicating it needs evaluation

From there, run it through the same pipeline as any generated item:

```
/backlog:full-quality-review US-007
```

Or audit all imported items at once:

```
/backlog:audit-items draft
```

---

## Common Workflows

### Import a sprint and evaluate all tickets before planning

```
/backlog:jira-pull sprint:current
/backlog:audit-items draft
```

The audit gives you a rapid readiness classification across all imported items before you decide which ones to take into sprint as-is and which need work.

### Someone created a ticket in Jira — bring it into the pipeline

```
/backlog:jira-pull PROJ-55
/backlog:full-quality-review US-{assigned-id}
```

### Push everything ready for the next sprint

```
/backlog:jira-push EP-002
```

Pushing the epic creates and links all `ready` child items in one call. Items that are not yet in `ready` are reported as skipped.

### A stakeholder edited a Jira ticket — pull the update

```
/backlog:jira-pull PROJ-42
```

The pull detects that Jira is newer, shows the diff, and asks whether to overwrite the local file. After pulling, the item returns to `draft` for re-evaluation if content changed.

---

## File Locations

| File | Purpose |
|---|---|
| `backlog/.config.json` | Jira connection config — gitignored, never committed |
| `backlog/.config.template.json` | Safe-to-commit template showing required structure |
| `backlog/backlog-items/uploaded/` | Items successfully pushed to Jira |
| `backlog/backlog-items/draft/` | Items imported from Jira (before evaluation) |

---

## Troubleshooting

**401 Authentication failed**
Verify `JIRA_API_TOKEN` is set in your current shell session (`echo $JIRA_API_TOKEN`). Tokens expire — regenerate at `id.atlassian.com/manage-profile/security/api-tokens`.

**404 Issue not found**
Check that the project key in `.config.json` matches the prefix in your issue keys exactly — it is case-sensitive.

**400 Jira rejected the payload**
Issue type names in `.config.json` must exactly match the types configured in your Jira project. Go to your project settings in Jira to see the exact names.

**Epic children not linking**
For next-gen (team-managed) projects the skill uses `parent.key`. For classic projects it uses `customfield_10014`. Verify your project type is set correctly in `.config.json`.

**Rate limit (429)**
Jira's API rate limit applies per user. Wait 60 seconds and retry. For large sprint imports, the skill fetches up to 50 issues per call — if your sprint is larger, use multiple JQL calls with pagination (`startAt` offset).

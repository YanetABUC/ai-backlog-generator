Set up and validate the Jira connection for this backlog. Saves configuration to backlog/.config.json and verifies the connection with a live API call.

---

## Overview

This skill writes `backlog/.config.json` from the template and verifies the connection. The API token is never written to disk — it must be set as the environment variable `JIRA_API_TOKEN`.

Run this once before using `/backlog:jira-push` for the first time.

---

## Step 1: Gather Configuration

Ask the user for:

1. **Jira base URL** — e.g. `https://yourcompany.atlassian.net` (no trailing slash)
2. **Project key** — the prefix used in issue keys, e.g. `PROJ` from `PROJ-42`
3. **Your Jira email** — the email you log into Jira with
4. **Project type** — Next-gen (team-managed) or Classic (company-managed)? If unsure, say "Next-gen is the default for new Jira projects created after 2019."
5. **Custom issue type names** (optional) — if your project uses different names than the defaults (Story, Bug, Task, Epic), list them

Then ask: "Is the `JIRA_API_TOKEN` environment variable already set in your shell?"

If not, tell the user:
> "Before proceeding, create a Jira API token at https://id.atlassian.com/manage-profile/security/api-tokens and set it in your shell:
> ```
> export JIRA_API_TOKEN=your_token_here
> ```
> Add this to your shell profile (~/.zshrc or ~/.bashrc) to make it permanent."

Wait for the user to confirm the token is set before continuing.

---

## Step 2: Write Config File

Write `backlog/.config.json` with the values provided:

```json
{
  "jira": {
    "baseUrl": "{BASE_URL}",
    "projectKey": "{PROJECT_KEY}",
    "email": "{EMAIL}",
    "projectType": "next-gen",
    "issueTypes": {
      "Epic": "Epic",
      "US": "Story",
      "BUG": "Bug",
      "SPK": "Story",
      "TSK": "Task"
    }
  }
}
```

Update `issueTypes` if the user provided custom names.

Remind the user: "`backlog/.config.json` is gitignored — it will not be committed."

---

## Step 3: Verify Connection

Run a live API call to confirm the credentials work and the project exists:

```bash
curl -s -o /dev/null -w "%{http_code}" \
  -u "${JIRA_EMAIL}:${JIRA_API_TOKEN}" \
  -H "Accept: application/json" \
  "{BASE_URL}/rest/api/2/project/{PROJECT_KEY}"
```

Substitute `{JIRA_EMAIL}` from the config and `{BASE_URL}`, `{PROJECT_KEY}` from the values provided.

- **200:** Connection successful. Tell the user: "Connected to Jira. Project `{PROJECT_KEY}` found. You can now use `/backlog:jira-push`."
- **401:** Tell the user: "Authentication failed. Verify that `JIRA_API_TOKEN` is set correctly and the email matches your Jira account."
- **404:** Tell the user: "Project `{PROJECT_KEY}` not found. Check the project key — it's case-sensitive and appears in your Jira issue keys (e.g. the `PROJ` in `PROJ-42`)."
- **Other:** Show the status code and tell the user to check the base URL and network connection.

---

## Step 4: Confirm

If the connection succeeds, tell the user:
> "Jira is configured. Your items in `backlog/backlog-items/ready/` and `backlog/epics/ready/` can now be pushed with `/backlog:jira-push {ID}`."
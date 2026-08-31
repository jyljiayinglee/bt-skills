---
name: setup
description: "First-run configuration for bt-skills. Collects your Jira project, Confluence space, and Workato workspace context, verifies your MCP connections, and writes local config that recipe-debugger and writer read at runtime. Triggers on '/setup', 'set up bt-skills', or automatically the first time recipe-debugger or writer can't find a config file."
---

# Setup — bt-skills configuration wizard

Nothing in this plugin is pre-configured for any specific team or person. Every skill reads its context from a local config file that only exists after you run this once. Nothing you enter here is shared with anyone else — it stays in your own local config file.

## When this runs

- Explicitly, via `/setup`
- Automatically, when `recipe-debugger` or `writer` looks for `.bt-skills/config.json` (relative to the current workspace root) and doesn't find it

## Step 1 — Check for existing config

Look for `.bt-skills/config.json` in the current workspace. If it exists, show the current values and ask whether the user wants to keep, update, or fully redo them. If it doesn't exist, proceed to Step 2.

## Step 2 — Collect context

Ask the user for:

1. **Jira project key(s)** — e.g. `BSYS`, `BTSUP` — which project(s) tickets should be filed/commented against
2. **Jira Cloud ID** — if unknown, look it up via the Jira MCP's site-resolution call and confirm with the user before saving
3. **Confluence space key** — where formal docs (runbooks, changelogs, UAT instructions, etc.) should be written
4. **Recipe/ticket prefix conventions**, if the team uses them — e.g. `[PON]`, `[ITAM]` — optional; skip if not applicable
5. **Recipe version-comment format**, if the team has one — e.g. `{TICKET}: {imperative} — {before → after}`; default to that format if the user has no existing convention

Do not guess or default any of these to a specific team's values — every field comes from what the user says, or is confirmed live against their own MCP connections.

## Step 2.5 — Guidelines location (full Confluence / hybrid / full local)

`writer` reads a guidelines source at runtime for doc templates and ticket/comment conventions — it doesn't ship with any baked in. Ask:

> "Where should your team's guidelines live — fully on Confluence, fully as a local file, or a mix of both?"

Genericized starter content for any of these comes from `templates/guidelines-starter.md` in this plugin (the same template/ticket/comment conventions your personal setup uses, with no team-specific examples baked in).

### Path 1 — Full Confluence

Everything lives on one Confluence page.

- **If they already have a page covering this:** ask for the page URL/title, confirm it resolves via the Confluence MCP, store it as `guidelines.source: "existing"`. Nothing is created.
- **If they don't have one yet:** offer to create it, always inside the Confluence space already set in `confluence.space_key` (Step 2) — never a different space. Default title: `bt-skills Documentation Guidelines`, top-level in that space unless the user names a parent page to nest it under. If granted a Confluence write, create it directly via their Confluence MCP and store the new page ID as `guidelines.source: "created"`. If not, hand them the rendered starter markdown to paste in themselves, then confirm the URL once it's up.

### Path 2 — Hybrid (Confluence + local)

Some guidelines live on Confluence, the rest live locally — e.g. a team-wide Confluence page for shared conventions, plus a local file for an individual's own shortcuts, or vice versa (an existing local file, with the Confluence piece still pending). Ask which pieces go where. Write both halves:

- The Confluence half follows Path 1's create/existing logic for whatever it covers.
- The local half is written to `.bt-skills/guidelines.md` (installer's own workspace — see Path 3) for whatever it covers.
- Store `guidelines.source: "hybrid"` plus both locations (`page_id`/`page_url` for the Confluence half, `local_path` for the local half), so `writer` knows to read both and merge them, and knows which topics come from which.

### Path 3 — Full local

No Confluence involved at all.

- **Where:** always `.bt-skills/guidelines.md`, in the installer's own current workspace — the same place `.bt-skills/config.json` lives. Never inside this plugin's repo, and not shared with anyone else by default (it's their own local file; sharing it with teammates, e.g. via their own team repo, is up to them).
- If the file doesn't exist yet, write it from `templates/guidelines-starter.md` verbatim, then tell the user to edit it directly — no MCP write, no approval gate, it's their own local file.
- If `.bt-skills/guidelines.md` already exists (prior run, or hand-authored), treat it as existing — ask whether to keep, replace, or fill in any starter sections it's missing.
- Store `guidelines.source: "local"` and the relative path in config.

In every path, the result in config is the same shape: one or two guidelines sources (Confluence page and/or local file path) that `writer` reads at runtime. What differs is only where the content lives and how it got populated.

## Step 3 — Verify MCP connections

Check that the user has working connections for:
- **Jira** — required for `writer`
- **Confluence** — required for `writer`'s doc mode
- **Workato / Recipe Code** — required for `recipe-debugger`

If a connection is missing or fails, tell the user which skill(s) won't work until it's set up, but continue — partial config is fine (e.g. a user who only needs `recipe-debugger` doesn't need Confluence configured).

## Step 4 — Write local config

Write `.bt-skills/config.json` in the current workspace root:

```json
{
  "jira": {
    "projects": ["BSYS", "BTSUP"],
    "cloud_id": "..."
  },
  "confluence": {
    "space_key": "..."
  },
  "guidelines": {
    "source": "existing | created | hybrid | local",
    "confluence": {
      "page_id": "...",
      "page_url": "..."
    },
    "local_path": ".bt-skills/guidelines.md"
  },
  "conventions": {
    "prefixes": {},
    "version_comment_format": "{TICKET}: {imperative} — {before → after}"
  },
  "configured_at": "YYYY-MM-DD"
}
```

Confirm the file was written and summarize what was configured.

## Notes

- This file is per-user and per-workspace — it is never committed to this plugin's repo, and never carries anyone else's context
- `recipe-debugger` and `writer` should each check for this file at the start of their own first step, and hand off to this skill automatically if it's missing rather than failing silently

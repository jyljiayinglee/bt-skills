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

## Step 2.5 — Guidelines page (existing / create / hybrid)

`writer` reads a Confluence guidelines page at runtime for doc templates and ticket/comment conventions — it doesn't ship with any baked in. Ask:

> "Does your team already have a documentation guidelines page on Confluence — something covering doc templates, ticket format, or comment conventions?"

**Path A — Existing, complete.** They already have a page that covers this. Ask for the page URL or title, confirm it resolves via the Confluence MCP, and store its page ID/URL in config as `guidelines.source: "existing"`. Nothing is created.

**Path B — None yet.** Offer to create a starter page for them, seeded from `templates/guidelines-starter.md` in this plugin — the same template/ticket/comment conventions your personal setup uses, genericized (no team-specific examples baked in).

- **Where:** always inside the Confluence space already set in `confluence.space_key` (Step 2) — never a different space. Default title: `bt-skills Documentation Guidelines`. Default placement: top-level in that space (no parent), unless the user names a parent page (e.g. their team's existing index/home page) to nest it under instead. Confirm the final title + placement with the user before writing.
- If they grant a Confluence write, create the page directly via their Confluence MCP at that location and store the new page ID as `guidelines.source: "created"`.
- If they'd rather not grant that write, hand them the rendered markdown from `templates/guidelines-starter.md` to paste in themselves at that same location, and ask them to confirm the page URL once it's up.

**Path C — Hybrid (partial existing).** They have *some* conventions (e.g. a ticket-description standard) but not full doc-template coverage, or vice versa. Ask which pieces they already have covered. For each gap:
- Compare against `templates/guidelines-starter.md`'s sections (General Conventions, Document Templates, Ticket Conventions, Comment Conventions, Version Comment, Cross-Cutting Style Rules)
- Offer to append only the missing sections to their existing page (again, direct write if granted, or hand off the markdown snippet for those sections if not)
- Store `guidelines.source: "hybrid"` plus the existing page ID, so `writer` knows this page is a merge and shouldn't be treated as fully self-authored

In all three paths, the result is the same shape in config: one Confluence page (or set of pages) that `writer` reads at runtime. What differs is only how it got populated.

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
    "space_key": "...",
    "guidelines": {
      "source": "existing | created | hybrid",
      "page_id": "...",
      "page_url": "..."
    }
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

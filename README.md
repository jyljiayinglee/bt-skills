# bt-skills

Installable Claude skills for BT Apps — recipe debugging, Jira/doc writing, and first-run setup. Works in both Claude Code and Cowork.

## Skills

| Skill | What it does |
|---|---|
| `setup` | First-run config wizard — collects your Jira project, Confluence space, and Workato context, verifies your MCP connections, writes local config |
| `recipe-debugger` | Diagnose a Workato recipe error → root cause, fix, and a ticket draft handoff |
| `writer` | Draft Jira tickets/comments (Ticket mode) or formal template-governed docs like runbooks and change logs (Doc mode), from your team's Confluence reference index |

## Install

**Prerequisites:** **Claude Code or Cowork only** — not plain claude.ai web chat. These skills need a real local filesystem (to write `.bt-skills/config.json` and, if you use it, `.bt-skills/guidelines.md`) and MCP tool access for Jira/Confluence/Workato, neither of which plain claude.ai provides. If you only use claude.ai without Code or Cowork, this plugin won't work for you.

You'll also need your own Jira and Confluence MCP connections already set up (ask BT Apps if you don't have these yet). A Workato/Recipe Code connection is only needed if you'll use `recipe-debugger`.

1. Add the marketplace and install the plugin:
   ```
   /plugin marketplace add <this-repo-url>
   /plugin install bt-skills
   ```
2. Run `/setup` once. It'll ask for:
   - Your Jira project key(s) (e.g. `BSYS`, `BTSUP`)
   - Your Confluence space key (where formal docs get written)
   - Any recipe/ticket prefix conventions your team uses (optional)
   - It verifies your Jira/Confluence/Workato connections along the way and tells you if any are missing — partial setup is fine if you only need one skill.
3. Setup writes a local `.bt-skills/config.json` in your current workspace. Nothing in it is shared with anyone else, and nothing about your team is pre-baked into the plugin itself — every installer configures their own context.
4. You're ready — try `/recipe-debugger` on a Workato error, or `/writer` to draft a ticket or doc.

**If you switch workspaces** (e.g. a different repo or folder), run `/setup` again there — config is per-workspace, not global.

**If your Jira/Confluence context changes** (new project, new space), run `/setup` again anytime to update it — it'll show your current values first and ask whether to keep, update, or redo them.

## Updates

```
/plugin marketplace update
```

## Notes

- All writes happen through your own Jira/Confluence/Workato connections — never anyone else's.
- Templates, conventions, and worked examples live on your team's Confluence reference index, not baked into this repo.

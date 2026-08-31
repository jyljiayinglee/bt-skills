# bt-skills

Installable Claude skills for BT Apps — recipe debugging, Jira/doc writing, and first-run setup. Works in both Claude Code and Cowork.

## Skills

| Skill | What it does |
|---|---|
| `setup` | First-run config wizard — collects your Jira project, Confluence space, and Workato context, verifies your MCP connections, writes local config |
| `recipe-debugger` | Diagnose a Workato recipe error → root cause, fix, and a ticket draft handoff |
| `writer` | Draft Jira tickets/comments (Ticket mode) or formal template-governed docs like runbooks and change logs (Doc mode), from your team's Confluence reference index |

## Install

```
/plugin marketplace add <this-repo-url>
/plugin install bt-skills
```

Then run `/setup` once to configure your Jira/Confluence/Workato context — nothing is pre-configured, and nothing you enter is shared with anyone else.

## Updates

```
/plugin marketplace update
```

## Notes

- All writes happen through your own Jira/Confluence/Workato connections — never anyone else's.
- Templates, conventions, and worked examples live on your team's Confluence reference index, not baked into this repo.

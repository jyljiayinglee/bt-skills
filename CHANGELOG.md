# Changelog

## v0.1.0 — 2026-08-31

- Initial scaffold: `.claude-plugin/marketplace.json` + `plugin.json`, `README.md`
- `setup` — first-run config wizard (Jira project, Confluence space, Workato context; verifies MCP connections; writes `.bt-skills/config.json`)
- `recipe-debugger` — direct fork of the personal `line-cook` skill, de-personalized (no `brain/` or vault references; reads recipe/prefix conventions from local config instead)
- `writer` — merged Ticket mode (Jira ticket descriptions, refinement, work-done comments, version comments) + Doc mode (formal templates: technical docs, UAT, test cases, rollback plans, change logs), forked from the personal `steward` skill's casual/formal tiers; templates and conventions pulled from the team's Confluence reference index at runtime, not baked into the repo

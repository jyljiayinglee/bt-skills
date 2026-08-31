# Changelog

## Unreleased — 2026-08-31

- Added MIT `LICENSE` and fleshed out `README.md` install steps (prerequisites, numbered walkthrough, per-workspace/context-change re-run guidance)
- Explicitly scoped to **Claude Code/Cowork only** in `README.md` — plain claude.ai lacks the filesystem and MCP access these skills depend on
- `setup` now supports **3 guidelines-location paths** instead of assuming Confluence-only: full Confluence (existing page, or newly created from `templates/guidelines-starter.md`), hybrid (split across a Confluence page + local `.bt-skills/guidelines.md`), and full local (`.bt-skills/guidelines.md` only, no Confluence)
- `writer` reads templates/conventions from whichever guidelines source(s) `setup` configured, instead of hardcoding a Document Type table and an Audience Calibration table directly in the skill — removes a drift risk against the actual source of truth
- Added `templates/guidelines-starter.md` — genericized fork of the personal `steward-guidelines.md`, seeded by `setup` when a team has no existing guidelines; kept BT/Workato-specific naming conventions (recipe prefix format, TYPE codes, `AHQ` workspace refs, Salesforce field naming), comment-convention rules, and a worked version-comment example, since this is an internal-only repo rather than a public-generic tool

## v0.1.0 — 2026-08-31

- Initial scaffold: `.claude-plugin/marketplace.json` + `plugin.json`, `README.md`
- `setup` — first-run config wizard (Jira project, Confluence space, Workato context; verifies MCP connections; writes `.bt-skills/config.json`)
- `recipe-debugger` — direct fork of the personal `line-cook` skill, de-personalized (no `brain/` or vault references; reads recipe/prefix conventions from local config instead)
- `writer` — merged Ticket mode (Jira ticket descriptions, refinement, work-done comments, version comments) + Doc mode (formal templates: technical docs, UAT, test cases, rollback plans, change logs), forked from the personal `steward` skill's casual/formal tiers; templates and conventions pulled from the team's Confluence reference index at runtime, not baked into the repo

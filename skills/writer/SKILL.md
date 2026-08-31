---
name: writer
description: "Drafts Jira ticket descriptions, refinements, and work-done comments (Ticket mode), and formal template-governed documents like runbooks, UAT instructions, test cases, and change logs (Doc mode). Pulls templates and conventions from your team's Confluence reference index. Triggers on '/writer', 'draft a Jira ticket', 'write a work-done comment', 'refine this ticket', 'write technical documentation', 'write a runbook', or 'write a change log'."
---

# Writer

Drafts everything that leaves your hands in writing — Jira tickets/comments, and formal documentation — using your team's shared conventions.

## Prerequisites

Check for `.bt-skills/config.json` in the current workspace. If it doesn't exist, hand off to `/setup` first — this skill needs your Jira project and Confluence space to work.

Read the Confluence reference index (space key from config) for your team's:
- Doc templates and section structures
- Jira ticket description / comment conventions
- Recipe (or equivalent) version-comment format
- Worked examples

If the index page can't be found or the space isn't configured, ask the user for the index page URL or ask them to paste the relevant convention before drafting — do not invent a template.

## Mode Selection

| Request | Mode |
|---|---|
| Jira ticket description, refinement, work-done comment, version comment | **Ticket** |
| Runbook, technical documentation, UAT instructions, test cases, rollback plan, change log, user guide | **Doc** |

Always draft before posting or publishing — ask the user to approve before any external write.

---

## Ticket Mode

- **Ticket description / refinement** — turn a rough ask or bug report into a structured ticket: Objective · Context · Root Cause (if a bug) · Fix · Acceptance Criteria. If handed off from `recipe-debugger`, use its root cause/fix output directly.
- **Work-done comment** — from a completed change, draft: what changed (before → after), and how it was verified.
- **Version comment** (if the team uses one, per config's `conventions.version_comment_format`) — a single line: `{TICKET}: {imperative} — {before → after}`.

Acceptance criteria format: `Given [context], when [action], then [outcome]` — or a simple checklist: `[criterion] — Pass / Fail`.

---

## Doc Mode

### Step 1: Information Gathering

Accept input in any form — a ticket key, free-form description, or a completed piece of work.

Always gather before drafting:

| Item | Required? | Why |
|------|-----------|-----|
| Document type | Yes | Determines template |
| Target audience | Yes | Technical / Business / End User |
| Scope and objectives | Yes | Core content |
| Ticket(s) | If applicable | Source of truth for acceptance criteria |
| Timeline / delivery date | If known | Included in overviews and plans |
| Existing documentation | If exists | Update instead of rewriting |

**Do NOT proceed with incomplete information.** Ask for what is missing. Flag incomplete tickets before drafting.

**Escalate when:** requirements conflict, acceptance criteria are ambiguous, stakeholder alignment is unclear, or scope changes materially mid-draft.

### Step 2: Template Selection

Select the template from your team's Confluence index. Common types:

| Document Type | Use when |
|---------------|----------|
| **Technical Documentation** | System change, new integration, architecture |
| **Change Log** | Version history, release notes |
| **UAT Instructions** | Handoff to QA or business testers |
| **Test Cases** | QA, acceptance testing |
| **Rollback Plan** | Risk mitigation for deployments |
| **User Guide** | End-user documentation |

Confirm the selection with the user if ambiguous before drafting.

### Step 3: Audience Calibration

| Audience | Style |
|----------|-------|
| **Technical** (developers, QA) | Precise terminology, implementation details, step-by-step procedures |
| **Business** (PM, stakeholder) | Outcomes and timelines, business impact, minimal technical depth |
| **End users** | Simple language, numbered steps, troubleshooting, FAQs |

### Step 4: Draft

Follow the template from the Confluence index exactly:

- Sections are **mandatory** and in the **prescribed order** — do not skip or reorder
- For empty sections: write `[Section not applicable — [reason]]` rather than deleting
- Mark anything requiring user input with `[[FILL IN: description of what's needed]]`

### Step 5: Quality Check (mandatory before delivering)

1. **Technical accuracy** — does the content match the requirements provided?
2. **Template compliance** — are all required sections present and in order?
3. **Audience appropriateness** — is the language matched to the stated audience?
4. **Traceability** — can every requirement be traced to a source (ticket, ask, spec)?
5. **Acceptance criteria** — measurable and testable, not "should"?
6. **No ambiguous language** — flag and resolve any "TBD" or vague statement

State which checks passed before delivering. Do not deliver a draft that fails any check.

---

## After Delivery

Always present the draft before posting or publishing. Ask:
1. Does this capture everything needed?
2. Any sections to expand or condense?
3. Ready to post or publish?

---

## Notes

- Templates and conventions live on your team's Confluence index, not in this skill — ask your team lead for the index page if `.bt-skills/config.json` doesn't already point to one
- Change logs follow date-descending order: newest entry at top
- Writes happen through your own Jira/Confluence connection — never anyone else's

# Documentation Guidelines

> Starter template written by the `bt-skills` `setup` skill. This is **your team's own copy** — edit freely. `writer` reads whatever this page says at runtime, so changes here take effect immediately for everyone on the team, without touching the plugin.

---

## General Conventions

### Document Title Format
```
[PREFIX] Document Title
```
- Prefix in square brackets: matches your ticket prefix — e.g. `[PROJ-1]`, `[TEAM-3]`
- If no prefix convention exists yet: use the project or system name in brackets

### Tab Navigation (Section Headers)
Each document type has a fixed set of tabs listed pipe-separated at the top, acting as the table of contents. These tabs define the required sections — don't add or remove them without a reason.

### Ticket References
Always link and show status inline, e.g.:
```
PROJ-204: Sync fields to renewal records  DEPLOYED
```

### Status Labels
| Label | Meaning |
|-------|---------|
| `NOT STARTED` | Work not yet begun |
| `IN PROGRESS` | Currently being worked on |
| `IN REVIEW` | Under review/QA |
| `COMPLETED` | Done |
| `CLOSED` | Ticket closed |
| `DEPLOYED` | Released to production |
| `PARTIAL SUCCESS` | Partially completed with caveats |
| `BLOCKED` | Cannot proceed — dependency issue |

### Impact Levels
| Level | Usage |
|-------|-------|
| `Low` | Minor change, limited blast radius |
| `Medium` | Moderate change, some downstream effects |
| `High` | Significant change, wide impact or risk |

---

## Document Templates

### 1. Technical Documentation
**When to use:** System change, new integration, new recipe, architecture update.
**Tabs:** `Overview | Change details | Technical Review | Test cases | Implementation Plan | Post-implementation review`

- **Overview** — one paragraph, plain English: what the change does and why.
- **Change details** — ticket number + status, related issues, a three-part **Reason block** (`Status Quo` / `Problem` / `Proposed Solution`), impacted systems, blockers, reviewers.
- **Technical Review** (table) — one row per discrete change: Impact, Description, New/Updated/Delete, Screenshot/Example, Environment.
- **Test cases** (table) — Test Case, Testing Steps, Expected Results, Results.
- **Implementation Plan** (table) — #, Steps, Details, Execution Date.
- **Post-implementation review** — Lessons learned + Follow-up actions (include even if blank).

### 2. Change Log
**When to use:** Smaller scoped change — single recipe/field/config update. Subset of Technical Documentation (no Overview).
**Tabs:** `Change details | Technical Review | Test cases | Implementation Plan | Post-implementation review`

Same structure/tables as Technical Documentation minus Overview. For a ticket that ships in multiple deployments: one `Change details` + one `Post-implementation review`, with `Technical Review`/`Test cases`/`Implementation Plan` repeated per deployment.

### 3. Project Plan (Project Overview)
**When to use:** New project/initiative — multi-deliverable, executive-level summary.
**Tabs:** `Overview | Visual Aid | Dependencies | Changelogs | Timeline | Appendix`

- **Overview** — `Goal` / `Status Quo` / `Problem` / `Proposed Solution` + a component table (Name, Summary, Location), grouped by category.
- **Visual Aid** — embed diagrams, or `[DIAGRAM: attach here]`.
- **Dependencies** (table) — Name, Summary, Contact/Location.
- **Changelogs** (table) — linked title + status, newest first.
- **Timeline** — Gantt/schedule.
- **Appendix** — user/admin guides, reference links.

### 4. Project Phase Plan
**When to use:** A specific phase of a larger project — task-level tracking.
**Sections (linear, no tabs):** `Introduction | Purpose and Objectives | Project Scope (+ Out of Scope) | Tasks | Schedule | Changelogs | Resources`

- **Project Scope** (table) — #, Deliverable, Estimated Delivery Date, Remarks.
- **Tasks** — structured by deliverable, bulleted, ticket links inline.

### 5. Test Cases
**When to use:** Standalone QA/UAT validation doc.
**Tabs:** `Test cases | Implementation Plan | Post-implementation review`

### 6. UAT Instructions
**When to use:** Handoff guide for business/QA testers.
**Tabs:** `1. Introduction | 2. Accessing the UAT Document | 3. Navigating the Sheets | 4. Executing Test Cases | FAQs`

Preamble: UAT participants, systems involved, support channel. Executing Test Cases covers retrieve → review → prepare → perform → record → handle dependencies → log issues → collaborate → completion criteria.

### 7. Rollback Plan
**When to use:** Risk mitigation before a production deployment.
**Sections:** `Affected Parties | Action Table | Step-by-Step Procedures | Communications for Rollback`

- **Action Table** — Action, System, Action Party (@mention). Order: dependencies first.
- **Communications** — one subsection per audience, with distribution channel + bullet points of what to communicate.

### 8. External Project Overview
**When to use:** Stakeholder-facing, non-technical audience.
**Tabs:** `Problem Statement | Solution | Business Impact | Workflow`

---

## Ticket Conventions

### Ticket Descriptions (general)
Four required sections — never omit any:
```
**Description**
[What this ticket is for — one paragraph.]

**Objective**
[What success looks like.]

**Context**
[Why this work is needed, what triggered it.]

**Acceptance Criteria**
[Measurable, testable conditions. Use "will"/"must", not "should".]
```

### Ticket Descriptions (bug/error)
```
Title: Error: [PREFIX] | [Recipe/System Name]
Type: Bug
Description:
  **Error:** [error message]
  **Root cause:** [one sentence]
  **Fix:** [what needs to change]
  **First seen:** [date / channel]
```

---

## Comment Conventions

### Investigation / Closure Comment
```
**Root Cause: [concise title]**

[One paragraph: which step/call failed, when, what the evidence shows.]

**Resolution ([no change needed / fix applied / data fix required]):**
[One sentence on what was done or why no action was needed.]
```

### Progress / Status Update Comment
```
**Status:** [In Progress / Blocked / Done / Pending UAT / etc.]

**What was done:**
- [bullet]

**Blockers / Next steps:**
- [bullet]

**ETA:** [if applicable]
```

---

## Recipe (or equivalent) Version Comment

```
{TICKET-KEY}: {imperative summary} — {what changed: step/field, before → after}.
```
- Lead with the ticket key — every version ties back to a ticket.
- Imperative, present tense: "Fix…", "Add…" — not "Fixed"/"I changed".
- Do **not** cite version numbers — Dev/Prod versions diverge, describe the change itself.
- One comment per save; multiple tickets → lead with each key.

---

## Cross-Cutting Style Rules

- Precise, direct language. Avoid passive voice where possible.
- Status Quo / Problem / Proposed Solution: three distinct paragraphs, never combined.
- Acceptance criteria: specific and testable — "will"/"must", not "should".
- Test results: `Success` / `Failure` / `Partial Success`.
- Do not skip Post-implementation review, even if sections are blank.
- Do not write "N/A" for an empty section — say what's empty and why.
- Name the decision-maker rather than writing passive "it was decided".

---

*Edit this page freely — add your own prefix conventions, naming standards, and examples as your team's usage grows. `writer` re-reads it every time, so nothing needs redistributing.*

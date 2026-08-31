---
name: recipe-debugger
description: "Diagnose Workato recipe errors. Paste an error (from a Slack error channel, a support ticket, or direct observation) and optionally the recipe JSON export. Produces root cause, fix recommendation, and a Jira ticket draft if needed. Triggers on '/recipe-debugger', 'investigate this error', 'what's wrong with this recipe', or when debugging a Workato recipe failure."
---

# Recipe Debugger

Diagnoses Workato recipe errors: root cause, fix, and a ticket draft if one's warranted.

## Prerequisites

Check for `.bt-skills/config.json` in the current workspace. If it doesn't exist, hand off to `/setup` first — this skill needs your Jira project key to draft tickets.

## URL Fast-Path

If the user provides a Workato URL in any of these forms:
- `app.workato.com/recipes/{recipe_id}/job/{job_id}` — job URL
- `app.workato.com/recipes/{recipe_id}` — recipe URL

Do the following **before** asking for anything else:
1. Extract `{recipe_id}` from the URL
2. Call `Search Recipes` (Recipe Code MCP) with `recipe_id` to resolve the workspace ID and confirm the recipe name
3. Call `Get Recipe Code` (Recipe Code MCP) with `recipe_id` + `workspace_id` — this replaces the need for a pasted recipe JSON export
4. Proceed directly to Step 3 (Locate the Failing Step) using the fetched code, skipping the "ask for recipe JSON" part of Step 1

If a job URL is provided, also note the `{job_id}` — include it in the ticket draft as the failing job reference.

## Step 1: Collect the Error

Ask the user for (if not already provided):

1. **The error message** — exact text, including any step number, connector name, or exception type
2. **The recipe name or prefix**, if the team uses recipe-naming conventions (check `.bt-skills/config.json` for known prefixes)
3. **The recipe JSON or a Workato URL** — either paste the JSON export from Workato UI, or provide a job/recipe URL and the Recipe Code MCP will fetch the code automatically

If the user provides a ticket key instead, note it and proceed with whatever error context is available.

## Step 2: Parse the Recipe JSON

If recipe JSON is provided, parse it as follows:

**Unwrap:** The export wraps everything in a `{ "message": "..." }` envelope where the value is a stringified JSON. Parse the inner JSON first.

**Extract:**
- `name` + `keyword` from the root → recipe type (trigger vs action) and provider
- `input.description` from the trigger step → rich markdown spec of the recipe. Read it fully — purpose, inputs, outputs, error handling behaviour
- `block[]` → ordered steps. For each step, extract:
  - `number` — step index
  - `provider` — which connector (e.g. `workato_recipe_function`, `salesforce`, `slack`)
  - `name` — action name
  - `description` — human-readable label
  - `input` — input data (look for datapill references `_dp(...)` which show data flow)
- `extended_input_schema` / `extended_output_schema` on each step → data contracts

**Reconstruct the flow in plain English:**
```
Step 0 (Trigger): [what triggers this recipe]
Step 1: [connector] — [action] — [key inputs]
Step 2: [connector] — [action] — [key inputs]
...
```

If no JSON is provided, reconstruct what you can from the recipe name and error message alone.

## Step 3: Locate the Failing Step

From the error message, identify:
- Which step number failed (Workato errors usually include a step number or line reference)
- Which connector and action was executing
- What data was being processed at that point (use the input schema and datapill paths to infer)

## Step 4: Diagnose

Work through the most likely causes in this order:

**Connector / API issues:**
- Rate limits or quota exceeded
- Auth token expired or revoked
- API endpoint changed or deprecated
- Third-party system downtime

**Data issues:**
- Null or missing required field — check which field is required vs optional in the schema
- Type mismatch — e.g. string passed where integer expected (`render_input`/`parse_output` conversions)
- Empty array where at least one item is required
- Record not found (lookup by ID returned nothing)

**Recipe logic issues:**
- Datapill path is broken (source step output schema changed)
- Conditional branch routing incorrectly
- Loop processing a null list

**Environment issues:**
- DEV vs PROD config difference (wrong IDs, wrong endpoints)
- Data warehouse table/schema missing or access revoked
- Third-party UI component version mismatch (e.g. Slack block kit)

## Step 5: Produce the Investigation Report

Output in this format:

---

**Recipe:** `Recipe Name` (+ prefix, if the team uses one)
**Failing Step:** Step N — [connector]: [action]

**Error:**
> [exact error message]

**Root Cause:**
[1–3 sentences. Be specific — name the field, the record, the connector, the schema mismatch.]

**Fix:**
[Concrete action. What to change, where, and how. If it requires checking something in Workato UI or a connected system, say exactly what to look for.]

**Estimated Scope:** Inline fix / Recipe config change / Downstream schema change / Third-party dependency

---

## Step 6: Ticket Assessment

Determine whether this warrants a new ticket:

- **Create ticket if:** The fix requires recipe changes, schema updates, or coordination with another person/team
- **Skip ticket if:** It's a transient error (one-off API blip, now resolved), already tracked in an open ticket, or the fix is a one-line config change you can do immediately

If a ticket is needed, hand off the draft to `/writer` (ticket mode) with the root cause, fix, and first-seen context — `writer` owns ticket formatting and posting.

## Notes

- This skill is for Workato recipe errors only. For general code debugging (scripts, connectors, build pipelines, APIs), use your own team's debugging tooling instead.
- For data-warehouse errors: always check whether the table/schema exists and whether the recipe's service account still has access — this is the most common root cause.
- For Salesforce lookup failures: check whether the lookup field is the right ID type and whether the record exists in the target environment (DEV vs PROD).

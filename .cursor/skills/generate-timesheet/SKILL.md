---
name: generate-timesheet
description: Generate a timesheet or work summary from Jira issues assigned to the user. Queries Jira via Atlassian MCP for issues updated in a date range, then formats ticket key, status, and short task descriptions. Use when the user asks for a timesheet, weekly report, status update, "what I worked on," or Jira work summary from a board or project.
---

# Generate Timesheet from Jira

Generate a concise timesheet by pulling assigned Jira issues updated in a given period and formatting them for status reports or time tracking.

## When to Use

- User asks for a **timesheet**, **weekly report**, or **status update**
- User wants "what I worked on" from **Jira** or a specific **board/project**
- User provides a Jira board or project URL and wants issues summarized

## Prerequisites

- **Atlassian (Jira) MCP** must be enabled and authenticated to the user's Jira site.
- If the user shares a board URL (e.g. `.../jira/software/c/projects/PROJ/boards/123?assignee=...`), extract **project key** (e.g. `PROJ`) and optionally **assignee account ID** from the query string.

## Workflow

### 1. Resolve Atlassian context

- Call **getAccessibleAtlassianResources** (no args). From the result, take the resource whose `url` matches the user's Jira site (e.g. `https://counsl.atlassian.net`) and use its `id` as **cloudId**.
- Call **atlassianUserInfo** (no args) to get the current user's **accountId** for "assigned to me" when the user does not specify another assignee.

### 2. Build JQL

- **Assignee**: Use `assignee = "<accountId>"`. If the user provided an assignee in the board URL (`assignee=...`), use that; otherwise use the accountId from atlassianUserInfo.
- **Date range**: Default to issues **updated in the last 5 days**. If the user says "this week," "last 7 days," or "last N days," use:
  - Last 5 days: `updated >= -5d`
  - Last 7 days: `updated >= -7d`
  - This week (approximate): `updated >= startOfWeek()`
- **Project**: If the user specified a project or board URL, add `project = "<PROJECT_KEY>"` (e.g. `project = ECOMM`).
- **Order**: `ORDER BY updated DESC`.
- **Example**:  
  `project = ECOMM AND assignee = "6230c1535b6d710070a12e1b" AND updated >= -5d ORDER BY updated DESC`

### 3. Query Jira

- Call **searchJiraIssuesUsingJql** with:
  - **cloudId**: from step 1
  - **jql**: from step 2
  - **maxResults**: 50–100
  - **fields**: `["summary", "description", "status", "updated"]`

### 4. Format each issue

For each issue, output exactly in this form:

```text
{{TICKET-KEY}} - {{STATUS}}
 - {{TASK-DESCRIPTION1}}
 - {{TASK-DESCRIPTION2}}
```

- **TICKET-KEY**: Issue key (e.g. `ECOMM-2361`).
- **STATUS**: `status.name` (e.g. Done, In Progress, Ready For QA).
- **TASK-DESCRIPTION1 / TASK-DESCRIPTION2**: Two one-line bullet points summarizing what was done. Derive from **summary** and **description**:
  - First line: Short summary of the work (one line, no line breaks).
  - Second line: One more concrete detail (implementation detail, outcome, or scope)—one line, max ~100 chars.
- Strip Markdown, links, and code blocks from description text; keep plain language only.

### 5. Emit the timesheet

- Add a short header if useful (e.g. project name, date range, assignee).
- List all issues in the format above, with a blank line between issues.
- Optionally end with a one-line note: "Source: Jira, project X, assignee Y, updated in last N days."

## Output template (copy this structure)

```text
**Project / board** – Updated in last N days

{{TICKET-###}} - {{STATUS}}
 - {{TASK-DESCRIPTION1}}
 - {{TASK-DESCRIPTION2}}

{{TICKET-###}} - {{STATUS}}
 - {{TASK-DESCRIPTION1}}
 - {{TASK-DESCRIPTION2}}
```

## Example

**Input**: "Timesheet from ECOMM board, assigned to me, last 5 days"

**Output**:

```text
**ECOMM – Updated in last 5 days**

ECOMM-2361 - Done
 - Update service status from pending to incomplete for Square Space initial report.
 - Implement order task status logic (FF, jurisdiction, journey) for orders-api.

ECOMM-2328 - Ready For QA
 - Autodiscount API integration to replace hardcoded service products.
 - Pull rule config from product-catalog-api, add unit and integration tests.
```

## Edge cases

- **No issues found**: Say "No Jira issues found for the given project/assignee and date range" and suggest widening the range or checking project/board.
- **User gives only a board URL**: Extract project key from the path (`/projects/<KEY>/`) and, if present, assignee from `assignee=` in the query string; use these in JQL.
- **Multiple Jira sites**: Use the cloudId for the site that matches the URL the user mentioned (e.g. counsl.atlassian.net).

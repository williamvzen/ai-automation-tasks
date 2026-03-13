# Timesheet Agent

**Experiment frontline** for testing new ideas and automating as many workflows as possible. This repo is a living lab: reusable skills, MCP integrations, agents, and custom tools are added and refined here for use across **Cursor, Claude, Codex, Copilot**, and other AI coding environments.

---

## Current state

### Skills / capabilities

| Skill | Description |
|-------|-------------|
| **Format calendar meetings** | Converts raw calendar/meeting paste (between `---INPUT---`) into a day-grouped list of meeting titles only. Use when pasting calendar data, or when you want “calendar to list” / “meetings by day” output. |
| **Generate timesheet** | Builds a timesheet or work summary from **Jira** issues assigned to you. Uses Atlassian MCP to query issues updated in a date range, then formats ticket key, status, and short task descriptions. Use for weekly reports, status updates, “what I worked on,” or Jira work summaries from a board or project. |

### Dependencies

- **Atlassian (Jira) MCP** — required for the timesheet skill (Jira search, user context). Use wherever MCP is supported (e.g. Cursor, Claude Desktop, etc.).

---

## Incoming / roadmap

- **More MCP integrations** — additional MCP servers (e.g. Slack, Figma, LaunchDarkly, etc.) as needed for workflows.
- **More agents & skills** — new skills and agent flows (e.g. meeting notes → tasks, spec → backlog, triage, status reports to Confluence), portable across environments.
- **Workflow automation** — end-to-end flows (e.g. calendar + Jira → one report, meeting notes → Jira tickets).
- **Custom tools** — scripts, CLIs, or small services that plug into skills or MCP.
- **Documentation & conventions** — rules, skill templates, and patterns so new skills and agents are easy to add and reuse in any supported env.

---

## Structure

```
.cursor/skills/               # Cursor implementation (SKILL.md format)
  format-calendar-meetings/   # Calendar paste → day-grouped meeting list
  generate-timesheet/         # Jira issues → timesheet (via Atlassian MCP)
```

Skill instructions and patterns are written to be **environment-agnostic** so they can be adopted in Claude, Codex, Copilot, or other AI coding tools (e.g. as custom instructions, rules, or plugins).

Contributions and experiments welcome — add skills, wire new MCPs, or propose new workflows via issues or PRs.

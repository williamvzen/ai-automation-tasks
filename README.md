# Timesheet Agent

> **This repository is no longer maintained.** Active development, additional flows, agents, skills, and integrations now live in **[zb-orchestrator](https://github.com/williamvzen/zb-orchestrator)** — use that project going forward.

**Experiment frontline** for testing new ideas and automating as many workflows as possible. This repo was a living lab: reusable skills, MCP integrations, agents, and custom tools for use across **Cursor, Claude, Codex, Copilot**, and other AI coding environments. Historical content below describes the last state of this archive.

---

## Successor: zb-orchestrator

**[https://github.com/williamvzen/zb-orchestrator](https://github.com/williamvzen/zb-orchestrator)** — supports more workflows, agents, skills, and related automation than this repo.

---

## Current state (archived)

### Skills / capabilities

| Skill | Description |
|-------|-------------|
| **Generate timesheet** | Builds a timesheet or work summary from **Jira** issues assigned to you. Uses Atlassian MCP to query issues updated in a date range, then formats ticket key, status, and short task descriptions. Use for weekly reports, status updates, “what I worked on,” or Jira work summaries from a board or project. |

### Dependencies

- **Atlassian (Jira) MCP** — required for the timesheet skill (Jira search, user context). Use wherever MCP is supported (e.g. Cursor, Claude Desktop, etc.).

---

## Structure

```
.cursor/skills/               # Cursor implementation (SKILL.md format)
  generate-timesheet/         # Jira issues → timesheet (via Atlassian MCP)
```

Skill instructions and patterns were written to be **environment-agnostic** so they can be adopted in Claude, Codex, Copilot, or other AI coding tools (e.g. as custom instructions, rules, or plugins).

For new work, use **[zb-orchestrator](https://github.com/williamvzen/zb-orchestrator)**.

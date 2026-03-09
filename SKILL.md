---
name: subagent-spawn-skill
description: Spawn and manage isolated sub-agents in OpenClaw for parallel or specialized task execution. Use when a workflow should be delegated to a dedicated agent with its own model, workspace, and timeout.
tags: [subagent, orchestration, openclaw, multi-agent, parallel, automation]
---

# Subagent Spawn Skill

## Purpose

Enable the main agent to spawn isolated sub-agents that execute tasks independently — with their own model, workspace, and timeout configuration.

---

## Basic Spawn

```javascript
sessions_spawn({
  runtime: "subagent",
  runTimeoutSeconds: 1800,
  cleanup: "delete",
  task: "Your full task description here"
})
```

### Spawn a Named Agent

```javascript
sessions_spawn({
  agentId: "your-agent-id",  // Must match an ID in agents.list[], NOT "main"
  runTimeoutSeconds: 1800,
  cleanup: "delete",
  task: "Your task instructions"
})
```

> Use `agentId` to spawn a specific agent. `cwd` only sets directory context — it does not select the agent.

---

## Agent Config (openclaw.json)

Register agents so they appear in the dashboard and can be referenced by ID:

```json
{
  "agents": {
    "list": [
      {
        "id": "main",
        "model": "your-main-model",
        "workspace": "/path/to/main/workspace"
      },
      {
        "id": "research",
        "model": "your-research-model",
        "workspace": "/path/to/agents/research-agent"
      },
      {
        "id": "email",
        "model": "your-email-model",
        "workspace": "/path/to/agents/email-agent"
      }
    ]
  }
}
```

Valid keys: `id`, `model`, `workspace` only.

---

## Agent Identity

Each subagent workspace should have an identity file (`MEMORY.md` or `IDENTITY.md`):

```markdown
# Identity

- **Name:** research-agent
- **Role:** Lead Research Specialist
- **Focus:** Find and qualify prospects
- **Emoji:** 🔍
```

The `name` field controls what appears in the dashboard and cron assignment.

---

## Agent Skills Directory

Skills are not inherited. Each subagent needs its own `/skills` directory:

```
agents/
  research-agent/
    skills/
      composio-ops/
      crm-sync/
  email-agent/
    skills/
      agentmail-email-skill/
```

---

## Cron Assignment

Tie a recurring cron job to a specific subagent:

```json
{
  "name": "research-cron",
  "payload": {
    "kind": "agentTurn",
    "agentId": "research",
    "message": "Your task instructions",
    "model": "your-preferred-model"
  }
}
```

---

## Timeout Guide

| Task Type | Recommended Timeout |
|-----------|-------------------|
| Simple API call | 30–60s |
| Research + analysis | 300–600s |
| Multi-step workflow | 900–1800s |

---

## Known Issues

1. **Workspace isolation** — Subagents may inherit the parent's CWD. Embed all instructions directly in the task prompt as a workaround.
2. **Skills not inherited** — Each agent needs its own `/skills` directory.
3. **Cron agentId** — Cron may run under main agent context but associates correctly in the dashboard via `agentId`.

---

## Debugging

If a subagent fails:
1. Check session history for error messages
2. Verify tools in the agent's `TOOLS.md`
3. Confirm required env vars are accessible to the subagent

---

## Use Cases

- Parallel research across multiple data sources
- Dedicated email outreach agent running on its own model
- Background data processing without blocking the main agent
- Routing complex tasks to a more powerful model

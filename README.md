# Subagent Ops — Claude Agent Skill

Spawn and manage isolated sub-agents within OpenClaw for parallelized task execution, background jobs, and specialized workflows.

---

## What This Skill Does

- Spawn one-shot or persistent sub-agent sessions
- Assign specific models and workspaces per agent
- Configure timeouts for task-appropriate execution
- Manage agent lifecycle: spawn, monitor, terminate

---

## Requirements

- OpenClaw runtime with `sessions_spawn` tool available
- Agent definitions registered in `openclaw.json`

---

## Spawn Pattern

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
  agentId: "your-agent-id",  // ID from agents.list[], NOT "main"
  runTimeoutSeconds: 1800,
  cleanup: "delete",
  task: "Your task instructions"
})
```

> **Important:** Use `agentId` to spawn a specific agent — not `cwd`. The `cwd` param only sets directory context, not which agent runs.

---

## Agent Config (openclaw.json)

Register agents so they appear in the dashboard:

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

**Valid keys:** `id`, `model`, `workspace` — no other fields.

---

## Agent Identity

Each subagent workspace should have an identity file (`MEMORY.md` or `IDENTITY.md`):

```markdown
# Identity

- **Name:** email-outreach
- **Role:** Email Outreach Specialist
- **Focus:** Send emails to prospects
- **Emoji:** :e_mail:
```

The `name` field controls what appears in the dashboard and cron assignment.

---

## Agent Skills Directory

Each subagent needs its own `/skills` directory. Parent agent skills are **not** inherited:

```
agents/
  research-agent/
    skills/
      composio-ops/
      crm-sync/
  email-agent/
    skills/
      agentmail-ops/
```

---

## Cron Assignment

Associate a cron job with a specific subagent:

```json
{
  "name": "research-cron",
  "payload": {
    "kind": "agentTurn",
    "agentId": "research",
    "message": "Your task instructions here",
    "model": "your-preferred-model"
  }
}
```

---

## Timeouts by Task Type

| Task Type | Recommended Timeout |
|-----------|-------------------|
| Simple API calls | 30–60s |
| Research + analysis | 300–600s |
| Multi-step workflows | 900–1800s |

---

## Known Issues

1. **Workspace isolation** — Subagents may inherit parent's CWD. Embed all instructions directly in the task prompt as a workaround.
2. **Skills not inherited** — Each agent needs its own `/skills` directory.
3. **Cron agentId** — Cron may run under main agent but associates correctly in the dashboard via `agentId`.

---

## Debugging

If a subagent fails:
1. Check session history for error messages
2. Verify tools available in the agent's `TOOLS.md`
3. Confirm required env vars are accessible to the agent

---

## Use Cases

- Parallel research across multiple sources
- Dedicated email outreach agent
- Background data processing
- Specialized model routing (fast model for simple tasks, powerful for complex)

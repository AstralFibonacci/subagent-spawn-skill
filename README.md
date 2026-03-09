# subagent-spawn-skill

An agent skill for spawning and orchestrating isolated sub-agents within [OpenClaw](https://openclaw.ai). Enables parallel task execution, background jobs, and specialized agent routing.

## What it does

- Spawn one-shot or persistent sub-agent sessions
- Assign dedicated models and workspaces per agent
- Configure timeouts appropriate to task complexity
- Manage agent lifecycle: spawn, monitor, terminate

## When to use this skill

- A task should be parallelized across multiple specialized agents
- You need a background job that runs independently of the main agent
- You want to route specific tasks to agents with different models or permissions

## Requirements

| Requirement | Details |
|-------------|---------|
| Runtime | OpenClaw with `sessions_spawn` tool |
| Agent config | Agents defined in `openclaw.json` |

## Structure

```
subagent-spawn-skill/
├── README.md     ← you are here (overview)
└── SKILL.md      ← full implementation guide for agents
```

## Quick example

```javascript
sessions_spawn({
  agentId: "research",
  runTimeoutSeconds: 1800,
  cleanup: "delete",
  task: "Research 5 leads and return structured data"
})
```

See [`SKILL.md`](./SKILL.md) for full configuration and patterns.

## Tags

`ai-agent` `subagent` `orchestration` `parallel` `openclaw` `multi-agent` `automation` `agent-skill` `workflow`

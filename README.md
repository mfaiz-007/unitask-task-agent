# Unitask Agent

Start finishing tasks instead of just organizing them: connect any AI agent to Unitask (unitask.app) to manage and do your tasks with secure prioritization, tags, time blocks and more.

Unitask is now in **public beta**. Anyone can sign up at `https://unitask.app` and create a scoped API token.
Recommended runtimes: OpenClaw, Codex IDE, Claude Code, and any MCP-compatible client.

## What it does

- Read tasks with token-efficient retrieval controls (`fields`, `compact`, description shaping)
- Create/update tasks (including subtasks via `parent_id`)
- Move subtasks between parent tasks
- Merge parent tasks (with preview first)
- Create/edit/delete tags
- Add/remove tags on tasks
- Soft-delete tasks
- Time-block your day (writes `scheduled_start` + `duration_minutes`)
- Read/update settings for onboarding and workflow preferences

## What it helps you achieve

- See only what matters right now (today, upcoming, recurring, high-priority slices)
- Cut token usage for agent workflows by returning smaller task payloads first
- Turn high-level intent into concrete actions (create, re-prioritize, schedule, close loops)
- Keep change risk low with dry-run-first for move/merge operations

## What it does not do yet

- No single-call bulk status update endpoint (agents should iterate task IDs)
- No undo endpoint for destructive operations
- No native "explain why this priority was chosen" inference endpoint (agents apply your own policy)

## Setup

1. Sign up at `https://unitask.app` (public beta) if you do not already have an account.
2. In Unitask: `Dashboard -> Settings -> API` create an API token with scopes you need.
3. In your AI app / MCP client config, store token as `UNITASK_API_KEY` (never paste tokens in chat).
4. Optional but recommended one-time setup: set account timezone via `update_settings({ settings: { timeZone: "<IANA>" } })`.

## Hosted MCP endpoint

- URL: `https://unitask.app/api/mcp`
- Header: `Authorization: Bearer <UNITASK_API_KEY>`

## First 5 prompts

1. "What are my tasks for today, including overdue, sorted by priority? Use compact output."
2. "Show my upcoming tasks for the next 7 days using fields id,title,priority,due_date."
3. "Create a task: Draft beta launch update, due tomorrow at 5pm. Then tag it launch."
4. "Move subtask <task_id> under parent <parent_id>; preview first, then apply."
5. "Merge parent <source_parent_id> into <target_parent_id>; show dry-run summary before apply."

## `list_tasks` quick controls

- Views/time windows: `view`, `tz` (optional override), `window_days`
- Date filters: `due_from`, `due_to`, `start_from`, `start_to`
- Targeted selectors: `recurring_only`, `priority_buckets`, `has_scheduled`, `has_start_date`, `has_due_date`
- Response shaping: `fields`, `compact=compact|minimal`, `include_description`, `description_max_chars`, `description_ellipsis`

If `tz` is omitted, Unitask defaults to account timezone from `settings.timeZone`.

For full tool contracts, see:
- `SKILL.md` (complete MCP tool list + scope/safety rules)
- `USE_CASES.md` (practical scenario matrix)
- `EXAMPLES.md` (top intents with prompt + expected output shape)

## Examples Gallery

Prompt + expected-output gallery for top intents:
- `EXAMPLES.md`

## Client configuration examples

### Claude Code (remote HTTP MCP)

Create/edit `~/.claude.json`:

```json
{
  "mcpServers": {
    "unitask": {
      "type": "http",
      "url": "https://unitask.app/api/mcp",
      "headers": {
        "Authorization": "Bearer ${UNITASK_API_KEY}"
      }
    }
  }
}
```

### VS Code (remote HTTP MCP)

Create `.vscode/mcp.json` in your workspace:

```json
{
  "inputs": [
    {
      "id": "unitask_api_key",
      "type": "promptString",
      "description": "Unitask API key",
      "password": true
    }
  ],
  "servers": {
    "unitask": {
      "type": "http",
      "url": "https://unitask.app/api/mcp",
      "headers": {
        "Authorization": "Bearer ${input:unitask_api_key}"
      }
    }
  }
}
```

## Safety

- Use the smallest scope needed.
- Scope guidance: `read` for retrieval, `write` for create/update, `delete` only when required.
- Keep `dry_run=true` first for compound actions (move/merge/timeblock apply).
- Confirm deletes unless user explicitly requested deletion.
- Avoid over-fetching: start with compact/projection queries and expand only for tasks you will act on.

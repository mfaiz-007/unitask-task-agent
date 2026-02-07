---
name: "unitask-task-agent"
description: "Use when a user wants an AI agent to read, create, update, or delete Unitask tasks through their own API token. Supports both local CLI usage and MCP server setup."
---

# Unitask Task Agent

## Purpose

This skill helps AI agents operate a user's Unitask account with scoped API tokens.

Supported operations:
- List tasks
- Get one task
- Create task
- Update task status
- Delete task (soft delete)
- Read/update settings (optional onboarding setup)
- Plan day time blocks (preview/apply)

## Required setup

1. User creates a Unitask API token from `Unitask -> Dashboard -> Settings -> API`.
2. User stores token locally as `UNITASK_API_KEY` (recommended: `/Users/mfaiz/Developer/unified-repo/unitask/website/.env.local`).
3. Optional base URL override as `UNITASK_API_BASE_URL`.

Never ask users to paste full tokens in chat logs.

## Scope model

- `read`: required to read/list tasks.
- `write`: create/update tasks.
- `delete`: delete tasks.
- If `write` or `delete` is granted, `read` must also be granted.

## Repo-native commands

From this repository:

```bash
npm run unitask:api -- list --limit 20
npm run unitask:api -- get --id <task_id>
npm run unitask:api -- create --title "Plan today"
npm run unitask:api -- update-status --id <task_id> --status done
npm run unitask:api -- delete --id <task_id>
```

## MCP mode

Start MCP server:

```bash
npm run unitask:mcp
```

Exposed MCP tools:
- `list_tasks`
- `get_task`
- `create_task`
- `update_task_status`
- `delete_task`
- `get_settings`
- `update_settings`
- `plan_day_timeblocks`

## Hosted MCP (HTTPS)

If the Unitask app is deployed (Vercel), an MCP endpoint is available at:

- `https://<your-domain>/api/mcp`

Auth headers (pick one):
- `Authorization: Bearer <UNITASK_API_KEY>` (recommended)
- `X-Unitask-Api-Key: <UNITASK_API_KEY>`

## Safety rules

- Use smallest required scope for the requested action.
- Confirm destructive actions (delete) unless user explicitly asks to proceed.
- Prefer `status=done` over delete when user intent is completion, not removal.
- For `plan_day_timeblocks`, prefer preview (`apply=false`) first; only apply after user confirms.

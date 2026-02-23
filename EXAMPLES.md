# Unitask Agent Examples Gallery

Prompt gallery for top user intents. Each example includes a realistic prompt and expected output shape.

## 1) Today triage, token-light

Prompt:
`Show my today tasks, include overdue, sorted by priority. Keep response minimal with id,title,priority,due_date.`

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "priority": "critical|high|medium|low", "due_date": "ISO|null" }
  ],
  "meta": { "total": 0, "limit": 50, "offset": 0 }
}
```

## 2) Recurring high-priority unscheduled slice

Prompt:
`List recurring high/critical tasks that are not scheduled yet.`

Expected query shape:
`list_tasks(status=todo, recurring_only=true, priority_buckets=critical,high, has_scheduled=false, compact=compact)`

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "priority": "high", "is_recurring": true, "scheduled_start": null }
  ]
}
```

## 3) Create + tag

Prompt:
`Create a task called "Draft investor update", due tomorrow 17:00 local time, then tag it "work".`

Expected action sequence:
1. `create_task`
2. `list_tags` / `create_tag` (if needed)
3. `add_task_tag`

Expected output shape:
```json
{
  "created_task_id": "uuid",
  "tag_applied": true
}
```

## 4) Description-aware compact retrieval

Prompt:
`Give me upcoming tasks in compact mode, include descriptions truncated to 120 chars with ellipsis.`

Expected query shape:
`list_tasks(view=upcoming, compact=compact, include_description=true, description_max_chars=120, description_ellipsis=true)`

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "description": "string..." }
  ]
}
```

## 5) Missing due date hygiene

Prompt:
`Show todo tasks that do not have a due date so I can clean up planning metadata.`

Expected query shape:
`list_tasks(status=todo, has_due_date=false, fields=id,title,priority,start_date,due_date)`

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "due_date": null }
  ]
}
```

## 6) Subtask move with preview first

Prompt:
`Move subtask <subtask_id> to parent <new_parent_id>. Show dry-run summary first, then apply.`

Expected action sequence:
1. `move_subtask(dry_run=true)`
2. User confirmation
3. `move_subtask(dry_run=false)`

Expected output shape:
```json
{
  "dry_run": { "valid": true, "changes": ["..."] },
  "applied": true
}
```

## 7) Parent merge with safety gate

Prompt:
`Merge parent <source_parent_id> into <target_parent_id>. Do dry-run first.`

Expected action sequence:
1. `merge_parent_tasks(dry_run=true)`
2. User confirmation
3. `merge_parent_tasks(dry_run=false)`

Expected output shape:
```json
{
  "dry_run": { "valid": true, "conflicts": [] },
  "applied": true
}
```

## 8) Time-block day (preview then apply)

Prompt:
`Plan my tasks from 09:00 to 13:00 with 10-minute breaks. Preview first, then apply if it looks good.`

Expected action sequence:
1. `plan_day_timeblocks(apply=false)`
2. User confirmation
3. `plan_day_timeblocks(apply=true)`

Expected output shape:
```json
{
  "preview": [{ "task_id": "uuid", "scheduled_start": "ISO", "duration_minutes": 25 }],
  "applied_count": 0
}
```

## 9) Start-date planning window

Prompt:
`Show tasks starting between 2026-02-20 and 2026-02-25, only recurring ones.`

Expected query shape:
`list_tasks(start_from=2026-02-20, start_to=2026-02-25, recurring_only=true, status=todo)`

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "start_date": "ISO" }
  ]
}
```

## 10) Weekly review done slice

Prompt:
`List done tasks completed this week with title,priority,completed_at.`

Expected query shape:
`list_tasks(status=done, fields=id,title,priority,completed_at, limit=100)` then filter for this week in agent logic.

Expected output shape:
```json
{
  "data": [
    { "id": "uuid", "title": "string", "priority": "medium", "completed_at": "ISO" }
  ]
}
```

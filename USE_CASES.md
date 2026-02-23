# Unitask Agent Use Cases (Public Beta)

Unitask is in **public beta**. Anyone can sign up at `https://unitask.app`, create a scoped token in `Dashboard -> Settings -> API`, and connect an AI agent.

For worked prompt examples with expected outputs, see `EXAMPLES.md`.

## Retrieval and Prioritization (12)

| # | Use Case | Recommended Query/Tool | Support | Notes |
|---|---|---|---|---|
| 1 | Today tasks including overdue | `list_tasks(view=today,status=todo)` | Yes | Defaults to account timezone if `tz` omitted |
| 2 | Upcoming this week | `list_tasks(view=upcoming,window_days=7,status=todo)` | Yes | Defaults to account timezone if `tz` omitted |
| 3 | Due in next N days | `list_tasks(due_from=<ISO>,due_to=<ISO>,status=todo)` | Yes | Single call |
| 4 | Starting tomorrow | `list_tasks(start_from=<ISO>,start_to=<ISO>,status=todo)` | Yes | Single call |
| 5 | Root tasks only | `list_tasks(parent_id=null,status=todo)` | Yes | Parent-focused view |
| 6 | Subtasks of one parent | `list_tasks(parent_id=<parent_id>,status=todo)` | Yes | Child-focused view |
| 7 | Tag-focused slice | `list_tasks(tag_id=<tag_id>,status=todo)` | Yes | Tag workflows |
| 8 | Recurring tasks only | `list_tasks(recurring_only=true,status=todo)` | Yes | Routine reviews |
| 9 | Priority bucket slice | `list_tasks(priority_buckets=critical,high,status=todo)` | Yes | Triage-first |
| 10 | Scheduled tasks only | `list_tasks(has_scheduled=true,status=todo)` | Yes | Calendar handoff |
| 11 | Tasks missing due dates | `list_tasks(has_due_date=false,status=todo)` | Yes | Data hygiene |
| 12 | Token-light fetch | `list_tasks(compact=minimal,fields=id,title,priority,due_date)` | Yes | Minimal payload |

## Task Operations (12)

| # | Use Case | Recommended Query/Tool | Support | Notes |
|---|---|---|---|---|
| 13 | Create task | `create_task` | Yes | Root task |
| 14 | Create subtask | `create_task(parent_id=<parent_id>)` | Yes | Nested task |
| 15 | Update title/description | `update_task` | Yes | Partial update |
| 16 | Set/clear due date | `update_task(due_date=<ISO|null>)` | Yes | Date control |
| 17 | Set/clear start date | `update_task(start_date=<ISO|null>)` | Yes | Date control |
| 18 | Set/clear recurrence | `update_task(recurrence=<rule|null>)` | Yes | Recurring flow |
| 19 | Set/clear schedule | `update_task(scheduled_start=<ISO|null>,duration_minutes=<n>)` | Yes | Time blocking |
| 20 | Mark done/reopen | `update_task_status` | Yes | State change |
| 21 | Move subtask (safe) | `move_subtask(dry_run=true|false)` | Yes | Preview first |
| 22 | Merge parent trees (safe) | `merge_parent_tasks(dry_run=true|false)` | Yes | Preview first |
| 23 | Delete task tree | `delete_task` | Yes | Soft delete |
| 24 | Plan day window | `plan_day_timeblocks(apply=false|true)` | Yes | Preview/apply |

## Tags, Settings, and Gaps (8)

| # | Use Case | Recommended Query/Tool | Support | Notes |
|---|---|---|---|---|
| 25 | List tags | `list_tags` | Yes | Optional include_deleted |
| 26 | Create/rename/delete tag | `create_tag`, `update_tag`, `delete_tag` | Yes | Full tag lifecycle |
| 27 | Attach/detach tag | `add_task_tag`, `remove_task_tag` | Yes | Task-tag mapping |
| 28 | Read settings | `get_settings` | Yes | Profile + preferences |
| 29 | Update settings/quiz | `update_settings` | Yes | Partial update |
| 30 | Description-aware compact retrieval | `list_tasks(include_description=true,description_max_chars=<n>)` | Yes | Context-aware summaries |
| 31 | Bulk status updates in one call | repeated `update_task_status` | Partial | No batch endpoint yet |
| 32 | Undo last destructive operation | N/A | No | No undo endpoint yet |

## Practical Prompt Pack

1. "Show today tasks in compact mode, include overdue, and return only id,title,priority,due_date."
2. "Give me high-priority recurring tasks that are unscheduled."
3. "Create a task for tomorrow 5pm, then schedule it for 30 minutes."
4. "Move subtask `<id>` to parent `<id>` with a dry run, then apply if valid."
5. "Plan my tasks from 09:00 to 13:00 with 10-minute breaks, preview first."

## Safety Defaults

- Use least-privilege scopes (`read`, `write`, `delete`) per workflow.
- Use `dry_run=true` first for `move_subtask` and `merge_parent_tasks`.
- Confirm destructive actions (`delete_task`, `delete_tag`) before apply.
- Prefer compact/projection retrieval before full detail fetches.
- `tz` is optional; account timezone (`settings.timeZone`) is the default unless explicitly overridden.

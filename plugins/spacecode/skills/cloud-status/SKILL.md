---
name: cloud-status
description: Check status of cloud tasks. Use when user says "cloud status", "task status", "check tasks", "what's running", or wants to see active/recent cloud sandbox tasks.
---

# Cloud Status — Task Dashboard

Check the status of your cloud sandbox tasks.

## Process

1. **List active tasks.** Call the `remote_tasks` MCP tool (default: active only).

2. **Display dashboard.** For each task show:
   - Task ID
   - Status (running, pending, completed, failed, cancelled)
   - Repository
   - Prompt (truncated)
   - Created time

3. **Offer follow-up actions:**
   - **Details**: "Want details on a specific task?" → call `remote_task_status` with the task ID
   - **Cancel**: "Want to cancel a running task?" → call `remote_cancel` with the task ID
   - **History**: "Want to see completed tasks too?" → call `remote_tasks` with `all: true`
   - **Wait**: "Want to wait for tasks to finish?" → call `remote_wait_all` with task IDs

## Example Usage

User: "What cloud tasks are running?"

1. Call `remote_tasks`
2. Display: 2 active tasks (sandbox-abc [running], sandbox-xyz [pending])
3. Offer: "Want details on either task, or wait for them to complete?"

## Notes

- By default only shows active (running/pending) tasks
- Use `all: true` to include completed, failed, and cancelled tasks
- Task IDs are needed for status checks, cancellation, and waiting

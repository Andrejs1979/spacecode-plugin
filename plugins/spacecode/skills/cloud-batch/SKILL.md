---
name: cloud-batch
description: Run the same task across multiple repos in parallel. Use when user says "batch", "multi-repo", "across repos", "all repos", or wants to apply changes to several repositories at once.
---

# Cloud Batch — Parallel Multi-Repo Execution

Run the same task across multiple GitHub repos simultaneously. Each repo gets its own ephemeral sandbox.

## Process

1. **Get the repo list.** Ask the user for repos if not provided. Accept:
   - Comma-separated: `owner/repo1, owner/repo2, owner/repo3`
   - One per line
   - GitHub org pattern: "all repos in org-name" (list with `gh repo list org-name`)

2. **Get the prompt.** The same instructions apply to all repos unless the user specifies per-repo tasks.

3. **Dispatch in parallel.** Call the `remote_exec_batch` MCP tool:
   - `tasks`: Array of `{ repo, prompt, branch?, model? }` objects

   Example:
   ```json
   {
     "tasks": [
       { "repo": "owner/repo1", "prompt": "Update dependency X to v2.0" },
       { "repo": "owner/repo2", "prompt": "Update dependency X to v2.0" },
       { "repo": "owner/repo3", "prompt": "Update dependency X to v2.0" }
     ]
   }
   ```

4. **Report aggregated results.** Show per-repo:
   - Success/fail status
   - Exit code
   - Diff summary (truncated)
   - Errors if any

## Example Usage

User: "Update the README copyright year to 2026 across my-org/app, my-org/lib, and my-org/docs"

Call `remote_exec_batch` with 3 tasks, same prompt. Present: 3/3 succeeded, diffs showing year change.

## Notes

- All tasks run in parallel — total time is roughly the slowest task
- Each task is independent; one failure doesn't affect others
- Results include per-repo diffs but changes are NOT auto-pushed
- For large batches (10+ repos), consider running in groups

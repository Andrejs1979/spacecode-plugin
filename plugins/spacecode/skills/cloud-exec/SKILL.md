---
name: cloud-exec
description: Run a task in a cloud sandbox. Use when user says "run in cloud", "remote exec", "cloud sandbox", or wants to execute code/commands in an ephemeral environment.
---

# Cloud Exec — Run Task in Cloud Sandbox

Dispatch a prompt to an ephemeral cloud sandbox that clones a repo, runs Claude Code with your instructions, and returns logs + git diff.

## Process

1. **Determine the repo.** Use the current git remote if available:
   ```bash
   git remote get-url origin 2>/dev/null
   ```
   Convert SSH URLs (`git@github.com:owner/repo.git`) to `owner/repo` format.
   If no git remote, ask the user for the repo.

2. **Get the prompt.** If the user provided a task description, use it. Otherwise ask what they want done.

3. **Dispatch the task.** Call the `remote_exec` MCP tool:
   - `repo`: The GitHub repository (e.g. `owner/repo`)
   - `prompt`: The task instructions
   - `branch`: (optional) Git branch to check out before running
   - `model`: (optional) Claude model to use

4. **Report results.** The tool blocks until completion. Present:
   - Exit code and success status
   - Git diff (if any changes were made)
   - Logs (truncated if very long)

5. **On failure**, suggest:
   - Check if the repo exists and is accessible
   - Verify the prompt is clear and actionable
   - Try a simpler prompt to isolate the issue
   - Use `/cloud-status` to check task history

## Example Usage

User: "Run the test suite for owner/my-app in the cloud"

Call `remote_exec` with:
  - repo: `owner/my-app`
  - prompt: `Run the full test suite. Report pass/fail counts and any failures.`

## Notes

- Sandboxes are ephemeral — destroyed after task completion
- Code changes are returned as a git diff but NOT auto-pushed
- Tasks may take several minutes depending on complexity
- The sandbox has git, GitHub CLI, and Claude Code pre-installed

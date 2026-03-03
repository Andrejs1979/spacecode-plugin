---
name: cloud-review
description: Dispatch a code review to a cloud sandbox. Use when user says "cloud review", "remote review", "review my code remotely", or wants an independent code review from a separate Claude instance.
---

# Cloud Review — Remote Code Review

Dispatch a code review to a remote Claude Code instance running in an ephemeral sandbox. Gets an independent review of your code changes.

## Process

1. **Detect the repo and branch.** Run:
   ```bash
   git remote get-url origin 2>/dev/null
   git branch --show-current 2>/dev/null
   ```
   Convert SSH URLs to `owner/repo` format.

2. **Identify what to review.** Check for:
   - Uncommitted changes: `git diff --stat`
   - Unpushed commits: `git log origin/main..HEAD --oneline` (or appropriate base branch)
   - If there are uncommitted changes, warn the user they should commit and push first — the cloud sandbox clones from the remote.

3. **Construct the review prompt.** Build a prompt that:
   - Checks out the target branch
   - Diffs against the base branch (main/master)
   - Reviews for: bugs, logic errors, security issues, style problems, missing tests
   - Returns structured findings

   Example prompt:
   ```
   Review the code changes on branch '{branch}' compared to the main branch.

   Run: git diff main...HEAD

   Provide a structured review covering:
   1. Critical Issues — Bugs, security vulnerabilities, data loss risks
   2. Logic Errors — Incorrect behavior, edge cases, race conditions
   3. Style & Quality — Naming, structure, duplication, missing docs
   4. Missing Tests — Untested code paths, edge cases without coverage

   For each finding, include the file, line, and a specific fix suggestion.
   ```

4. **Dispatch via `remote_exec`.** Call the MCP tool with the repo, review prompt, and branch.

5. **Present findings** in a clear, actionable format.

## Example Usage

User: "Review my code before I create a PR"

1. Detect repo + branch
2. Call `remote_exec` with review prompt
3. Present structured findings

## Notes

- The review runs in a separate sandbox with a fresh clone
- Uncommitted local changes are NOT visible to the reviewer — push first
- The reviewer has full access to the repo context (not just the diff)
- Works best when changes are on a feature branch compared to main

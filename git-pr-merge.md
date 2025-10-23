# Git PR Merge

Goal: Take the last created PR for the current branch, merge it, and clean up the branch both locally and remotely.

## Behavior

1) Pre-flight validation
   - Determine current branch and ensure it's not the main/master branch.
   - Ensure working tree is clean.
   - Fetch latest: `git fetch --prune --tags`.

2) Find and validate PR
   - Query `gh pr list --head <current-branch> --json number,state,url,headRefName,baseRefName,isDraft,mergeable` for the current branch.
   - If no PR exists, abort with error message.
   - If multiple PRs exist, select the most recently created one.
   - Validate PR is mergeable and not draft. If not mergeable or draft, abort with appropriate error.

3) Merge the PR
   - Execute `gh pr merge <pr-number> --merge` to merge the PR.
   - If merge fails, abort with error message and current status.

4) Post-merge cleanup
   - Checkout main branch: `git checkout main` (or master if that's the default).
   - Fetch latest changes: `git fetch --prune --tags`.
   - Pull latest main: `git pull --ff-only`.
   - Delete local branch: `git branch -d <branch-name>`.
   - Delete remote branch: `git push origin --delete <branch-name>`.

## Optional arguments
- `--no-cleanup`: Skip branch deletion (both local and remote).
- `--force-delete`: Force delete local branch even if not fully merged (use `-D` instead of `-d`).

## Error handling
- If PR doesn't exist: "No PR found for current branch '<branch-name>'"
- If PR is not mergeable: "PR #<number> is not mergeable. Please resolve conflicts first."
- If PR is draft: "PR #<number> is still in draft. Use 'gh pr ready' first."
- If merge fails: "Failed to merge PR #<number>. Check the PR status and try again."
- If cleanup fails: "PR merged successfully but cleanup failed. Manual cleanup may be required."

## Examples
- `git-pr-merge` → merge last PR for current branch and clean up
- `git-pr-merge --no-cleanup` → merge PR but keep branches
- `git-pr-merge --force-delete` → merge PR and force delete local branch

## Tools
- Use only: execute_shell_command, get_project_vcs_status
- Shell commands limited to: git, gh
- Avoid: web_search, analysis tools, file editing tools

## Heuristics
- Always verify PR exists and is mergeable before attempting merge.
- Use merge strategy (not squash or rebase) to preserve commit history.
- Clean up both local and remote branches by default for hygiene.
- Provide clear error messages for common failure scenarios.

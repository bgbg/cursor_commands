# Git Work on Issue

Goal: Start work on a GitHub issue by creating a properly named branch and annotating the issue for the current project.

## Behavior
1) Fetch issue
   - Accept `--issue <number|url>` or positional `<issue>` as the first non-flag argument. If neither is provided, ask for the issue number.
   - Fetch issue and repo metadata in one call: `gh issue view <issue> --json number,title,state,labels,repository`.
   - Extract repository info from `repository.nameWithOwner` and `repository.defaultBranchRef.name`.
   - Verify the issue is open. Abort if closed.

2) Determine base branch
   - Use the repository default branch.
   - Fetch only the base branch: `git fetch origin <base-branch>:<base-branch>`.
   - Fast-forward the base branch to match remote.

3) Ensure clean working tree
   - If there are uncommitted changes, abort.

4) Derive branch name
   - Pattern: `[fix|develop|feature]/<issue-number>-<kebab-description>`.
   - `type` selection rules:
     - If labels contain `bug` or `bugfix` → `fix`.
     - If labels contain `feature` or `enhancement` → `feature`.
     - Else → `develop`.
   - `description` derives from the issue title or manual prompt. Sanitize to kebab-case, ASCII, max length 48.

5) Create and switch branch
   - If `--tree`: Create git worktree in `.trees/<issue-number>-<type>-<kebab-description>` and work there.
   - Otherwise: `git checkout <base>` then `git pull --ff-only`, then `git checkout -b <derived-branch>`.

6) Push and annotate
   - Push upstream: `git push -u origin <derived-branch>` so links are clickable in GitHub.
   - Comment body: `Starting work on this in branch [<derived-branch>](https://github.com/<owner>/<repo>/tree/<derived-branch>).`
   - Do not include any signature lines (e.g., `Signed-off-by`).
   - Add comment: `gh issue comment <issue> --body "<body>"`.

7) Output
   - Print the branch name, base, and the clickable URL: `https://github.com/<owner>/<repo>/tree/<branch>`.
   - If `--tree`: Print the worktree location: `.trees/<issue-number>-<type>-<kebab-description>`.

## Arguments
- `--issue <number|url>`: Target issue identifier (flag form).
- `<issue>`: Positional target issue identifier (number or GitHub issue URL).
- `--tree`: Create branch in git worktree at `.trees/<issue-number>-<type>-<kebab-description>`.

## Heuristics
- If the issue title starts with a ticket key like `ABC-123:`, strip the prefix and use the remainder for the slug, but keep the numeric GitHub issue id in the branch name.
- Collapse repeated hyphens and trim hyphens from ends when forming the slug.
- If the derived branch already exists locally or remotely, append a short timestamp suffix `-yyyymmddHHMM`.

## Tools
- Use only: execute_shell_command, get_project_vcs_status
- Shell commands limited to: git, gh
- Avoid: web_search, analysis tools, file editing tools

## Examples
- `git-work-on-issue 42` → reads issue, creates `fix/42-login-error` (if labeled `bug`), pushes, and comments with branch link.
- `git-work-on-issue --issue 42 --tree` → same as above but creates worktree in `.trees/4-feature-enhance-documentcollection-load-method`.
- `git-work-on-issue https://github.com/<owner>/<repo>/issues/42` → same as first example.

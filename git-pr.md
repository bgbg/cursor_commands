# Git PR

Goal: Create a well-formed GitHub Pull Request for the current project, ensuring the branch is pushed, metadata is set (title, body, reviewers, labels), and handling forks/drafts intelligently.

## Behavior
1) Pre-flight
   - Verify GitHub CLI is authenticated (`gh auth status`). If not, abort with instructions.
   - Determine repo, current branch, and default base (`gh repo view --json defaultBranchRef`).
   - Fetch latest: `git fetch --prune --tags`.
   - Ensure at least one commit ahead of base; if none, abort. Ensure working tree is clean.

2) Ensure remote and push
   - If no upstream: `git push -u origin <branch>`.
   - If upstream exists and branch is behind, warn and suggest `git pull --rebase`.

3) Detect existing PR
   - Query `gh pr view --json number,state,url,headRefName,baseRefName,isDraft` for the branch.
   - If an open PR exists and is not draft, print URL and exit.
   - If an open PR exists and is draft, note it for later processing and continue to step 4.

4) Title and body generation
   - Generate title:
     - Derive from the most representative commit subject between `<base>..HEAD`:
       - Prefer the most frequent Conventional Commit type; subject in imperative; ≤ 72 chars.
       - If branch matches `[A-Z]+-\d+`, prefix the title with that key.
   - Generate body:
     - Assemble summary, notable changes, risks, tests, checklist from commit messages and diff stats.
     - Never include emojis or `Signed-off-by` or any signature lines in the PR body.
     - **CRITICAL - Issue linking**: The PR body MUST include `Closes #<issue-number>` footer:
       - If `--issues` parameter provided: add `Closes #<number>` for each issue
       - Otherwise, infer issue number from branch name (e.g., `feature/ABC-123` → `Closes #123`)
       - If no issue number can be determined, ask user whether to proceed without issue link

5) Create PR
   - Execute `gh pr create` with generated `--title` and `--body`. Base is the repo default; set `--head` for forks if needed.
   - Print resulting PR URL.
   - Open in browser and copy URL to clipboard by default unless disabled by flags below.

6) Post-create actions
   - If an existing draft PR was detected in step 3, remove draft status with `gh pr ready` and print updated URL.

## Optional arguments
- `--issues "123,456"`: Explicitly specify issue numbers to close (adds `Closes #<number>` footers to PR body). If not provided, the command will try to infer the issue number from the branch name pattern (e.g., `feature/123-add-auth` → issue #123).
- `--no-open`: Do not open PR in browser after creation. (default: open)
- `--no-copy-url`: Do not copy PR URL to clipboard. (default: copy)

## Heuristics
- If an open PR already exists, avoid duplicate creation and exit early.

## Examples
- `git-pr` → create PR from current branch to default base, auto-generate title/body, infer labels, open URL.
- `git-pr --base release/1.5 --draft --reviewers "alice,bob" --labels "type/feat,size/M"` → draft PR with reviewers and labels.
- `git-pr --title "fix: handle empty payload" --issues "123" --ready` → explicit title, closes #123, ready for review.

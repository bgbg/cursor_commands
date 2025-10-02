# Git PR

Goal: Create a well-formed GitHub Pull Request for the TechN project, ensuring the branch is pushed, metadata is set (title, body, reviewers, labels), and handling forks/drafts intelligently.

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
   - Query `gh pr view --json number,state,url,headRefName,baseRefName` for the branch.
   - If an open PR exists, print URL and exit.

4) Title and body generation
   - Generate title:
     - Derive from the most representative commit subject between `<base>..HEAD`:
       - Prefer the most frequent Conventional Commit type; subject in imperative; ≤ 72 chars.
       - If branch matches `[A-Z]+-\d+`, prefix the title with that key.
   - Generate body:
     - Assemble summary, notable changes, risks, tests, checklist from commit messages and diff stats.
     - Include TechN-specific context: RAG functionality, prompt management, search improvements, web UI changes.
     - Never include `Signed-off-by` or any signature lines in the PR body.
     - Always mention that this PR closes the issue that was discovered, either explicitly via `--issues` parameter or by inferring from branch name pattern.

5) Create PR
   - Execute `gh pr create` with generated `--title` and `--body`. Base is the repo default; set `--head` for forks if needed.
   - Print resulting PR URL.
   - Open in browser and copy URL to clipboard by default unless disabled by flags below.

6) Post-create actions
   - None.

## Optional arguments
- `--issues "123,456"`: Close these issues (adds `Closes #...` footers). If not provided, the command will try to infer the issues from the branch name.
- `--no-open`: Do not open PR in browser after creation. (default: open)
- `--no-copy-url`: Do not copy PR URL to clipboard. (default: copy)

## TechN Project Context
- Focus on RAG chat application improvements
- Consider impact on prompt management system
- Include testing information for search functionality
- Mention any web UI changes or API modifications

## Heuristics
- If an open PR already exists, avoid duplicate creation and exit early.

## Examples
- `git-pr` → create PR from current branch to default base, auto-generate title/body, infer labels, open URL.
- `git-pr --base release/1.5 --draft --reviewers "alice,bob" --labels "type/feat,size/M"` → draft PR with reviewers and labels.
- `git-pr --title "fix: handle empty payload" --issues "123" --ready` → explicit title, closes #123, ready for review.

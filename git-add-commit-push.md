# Git Add Commit Push

Goal: Stage changes, generate a commit message, commit, and push for the current project.

## Behavior
1) Handle untracked files:
   - If untracked files exist and `--all` is not set, ask to include them or ignore.
   - If `--no-confirm` is set, default to adding untracked files.

2) Stage changes:
   - If including untracked: `git add -A`
   - Otherwise: `git add -u`

3) Check branch name for issue reference:
   - If branch name contains a numbered issue pattern (e.g., `issue-123`, `fix/issue-42`, `123-fix-bug`), extract the issue number
   - Ask whether to add issue reference to commit message
   - If user says "close issue <NUMBER>" or similar: add "Closes #<NUMBER>" to commit message
   - If user says "related to issue #<NUMBER>" or similar: add "Related to #<NUMBER>" to commit message

4) Generate commit message:
   - If `--message` provided: use it exactly
   - Default (simple mode): `<type>: <brief description>` using file paths and their names. If total diff is shorter than 1000 characters, use that to generate a more detailed message, unless --fast is set.
   - If `--detailed`: full conventional commit with body and analysis
   - Append issue reference if user confirmed in step 3

5) Commit and push:
   - Commit: `git commit -m "<message>"`
   - Push to upstream or set upstream if needed

## Arguments
- `--all`: Include untracked files
- `--message <msg>`: Use provided commit message
- `--fast`: Generate a simple commit message (faster)
- `--simple`: If total diff is shorter than 1000 characters, use that to generate a more detailed message
- `--detailed`: Generate detailed conventional commit (slower)
- `--confirm` and `--no-confirm` : whether to skip confirmation prompts (DEFAULT: --no-confirm)

## Performance
- **Default**: Simple `<type>: <description>` format (fast)
- **`--detailed`**: Full analysis with body (slower but comprehensive)

## Tools
- Use only: execute_shell_command, get_project_vcs_status
- Shell commands limited to: git, gh, ls, cat, echo, diff
- Avoid: web_search, analysis tools, file editing tools

## Session Behavior
After completing the git add-commit-push operation, for the rest of the session, do not add, commit, or push any changes unless explicitly asked to do so by the user.

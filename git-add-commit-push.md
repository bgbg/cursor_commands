# Git Add Commit Push

Goal: Stage changes, generate a commit message, commit, and push for the TechN project.

## Behavior
1) Handle untracked files:
   - If untracked files exist and `--all` is not set, ask to include them or ignore.
   - If `--no-confirm` is set, default to adding untracked files.

2) Stage changes:
   - If including untracked: `git add -A`
   - Otherwise: `git add -u`

3) Generate commit message:
   - If `--message` provided: use it exactly
   - Default (simple mode): `<type>: <brief description>` using file paths and their names. If total diff is shorter than 1000 characters, use that to generate a more detailed message, unless --fast is set.
   - If `--detailed`: full conventional commit with body and analysis

4) Commit and push:
   - Commit: `git commit -m "<message>"`
   - Push to upstream or set upstream if needed

## Arguments
- `--all`: Include untracked files
- `--message <msg>`: Use provided commit message
- `--fast`: Generate a simple commit message (faster)
- `--simple`: If total diff is shorter than 1000 characters, use that to generate a more detailed message
- `--detailed`: Generate detailed conventional commit (slower)
- `--confirm` and `--no-confirm` : whether to skip confirmation prompts (DEFAULT: --no-confirm)
- `--no-push`: Skip push step

## Performance
- **Default**: Simple `<type>: <description>` format (fast)
- **`--detailed`**: Full analysis with body (slower but comprehensive)

## TechN Project Context
- Focus on RAG chat application changes
- Consider prompt management, search functionality, web UI components
- Use appropriate conventional commit types: feat, fix, docs, test, refactor, perf, chore

## Tools
- Use only: execute_shell_command, get_project_vcs_status
- Shell commands limited to: git, gh, ls, cat, echo, diff
- Avoid: web_search, analysis tools, file editing tools

## Session Behavior
After completing the git add-commit-push operation, for the rest of the session, do not add, commit, or push any changes unless explicitly asked to do so by the user.

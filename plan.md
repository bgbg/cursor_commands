# Plan work on a task

## Purpose
Create a concise, implementation-ready plan that a developer can quickly review and hand off to an LLM for execution.

## Behavior

If `--from-issue <number|url>` is provided:
1. Fetch issue and repo data: `gh issue view <issue> --json number,title,state,body,comments,repository`
2. Verify issue is open. Abort if closed.
3. Analyze issue content: title, description, comments, dependencies, affected components
4. If `--ask-clarifications` (default): ask clarifying questions if needed, comment them on the issue
5. Generate plan following Plan Structure below
6. Write to `todo__<issue-number>.md`

Otherwise (general planning):
1. First argument must be the output file path
2. Use entire project as context
3. Generate plan following Plan Structure below
4. Write to the specified file (overwrite if exists)

**Sub-issue creation**: After generating plan, if Steps section includes sub-issues, ask user to create them on GitHub. If confirmed, run `gh issue create` for each and update plan file with actual issue numbers (replacing `#<ISSUE_NUMBER>` placeholders).

## Arguments
- `<output-file>`: Output file path (required in general mode)
- `--from-issue <number|url>`: Generate plan from GitHub issue (writes to `todo__<issue-number>.md`)
- `<issue>`: Positional issue identifier (alternative to --from-issue)
- `--ask-clarifications`: Ask for clarifications in issue mode (default)
- `--no-ask-clarifications`: Skip clarifications in issue mode

## Style
- Expert audience: crisp, skimmable
- Bullet lists over paragraphs
- Backticks for code elements: `file.py`, `function()`, `ClassName`
- LLM-optimized: deterministic, explicit, no pronouns
- Numbered flat lists over nested bullets
- No time estimates or emojis
- Sub-issues: basic markdown only (headings, bullets, backticks, links) for `gh issue create` compatibility

## Plan Structure

Required sections:
1. **Title**: one-line goal statement
2. **Issue Summary** (issue mode only): brief description from GitHub issue
3. **Assumptions**: key assumptions that materially affect scope or design
4. **Approach**: rationale, alternatives briefly noted
5. **Steps**: numbered, outcome-focused. For complex tasks, break into sub-issues with "Related to #<ISSUE_NUMBER>" (replaced after GitHub creation)

Optional sections (add only if they reduce ambiguity or risk):
6. **Scope and Non-Goals**: inclusions and explicit exclusions
7. **Requirements and Constraints**: functional, performance, security, compatibility
8. **Risks & Mitigations**: top risks with practical mitigations
9. **Dependencies**: code, data, services; note ownership/availability
10. **Open Questions**: Numbered questions (Q1, Q2, ...) with answer placeholders (A1: ..., A2: ...) for user to fill in.
11. **Verification & Acceptance Criteria**: tests, success metrics
12. **Deliverables**: artifacts to produce
13. **Rollout & Backout**: release steps, monitoring, rollback

## LLM-Friendliness
- Stable section headers, no "above/below" references
- Explicit entity references (files, functions, data)
- Specify inputs, outputs, constraints with limits when relevant
- Provide schema and example when structure matters
- Deterministic, idempotent steps
- Consistent terminology

## Examples
```bash
# General planning
plan output.md add cancel button to the home page  # writes to output.md

# Issue-based planning
plan --from-issue 42            # writes to todo__42.md
plan 42                         # same (positional)
plan 42 --no-ask-clarifications # skip clarifications
```

## Notes
- Output file is always required in general mode
- In issue mode: use only git/gh commands, focus on planning not implementation
- Honor compact output requests while preserving structure
- Ensure valid Markdown with proper heading hierarchy

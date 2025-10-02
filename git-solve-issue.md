# Git Solve Issue

Goal: Analyze a GitHub issue and build a comprehensive plan for implementation, including clarifications and task breakdown.

## Behavior
1) Resolve issue
   - Accept `--issue <number|url>` or positional `<issue>` as the first non-flag argument. If neither is provided, ask for the issue number.
   - Fetch repo metadata: `gh repo view --json nameWithOwner,defaultBranchRef`.
   - Fetch issue metadata using `gh issue view` and verify the issue is open. Abort if closed.

2) Analyze issue content
   - Read the issue title, description, and all comments thoroughly.
   - Understand the problem domain and technical requirements.
   - Identify dependencies and relationships to existing code.

3) Ask for clarifications (if enabled)
   - If `--ask-clarifications` (default): Act as a seasoned developer, analyze the issue comprehensively.
   - Ask for clarifications only if needed - don't ask for the sake of asking.
   - Focus on:
     - Unclear requirements or acceptance criteria
     - Missing context about existing functionality
     - Ambiguous technical specifications
     - Integration points with other systems
   - Document any clarifications by commenting on the issue.

4) Build implementation plan
   - Break down the issue into logical, implementable tasks.
   - Identify files and components that need modification.
   - Consider testing requirements and edge cases.
   - Estimate complexity and suggest implementation order.
   - Highlight potential risks or challenges.

5) Output comprehensive plan
   - Create a file `todo__<issue-number>.md` in the same directory as the command is run
   - Present a structured implementation plan with:
     - Overview of the problem
     - Task breakdown with clear deliverables
     - File/module changes required
     - Testing strategy
     - Potential blockers or dependencies
   - Format the output in clear, actionable sections.

## Arguments
- `--issue <number|url>`: Target issue identifier (flag form).
- `<issue>`: Positional target issue identifier (number or GitHub issue URL).
- `--ask-clarifications`: Ask for clarifications if needed (default).
- `--no-ask-clarifications`: Skip clarification step and proceed directly to analysis.

## Analysis Heuristics
- If the issue title starts with a ticket key like `ABC-123:`, use that for context but focus on the actual GitHub issue content.
- Look for existing similar implementations in the codebase for patterns.
- Consider backward compatibility and migration needs.
- Identify which components are likely to be affected based on the issue description.

## Tools
- Use only: execute_shell_command, get_project_vcs_status
- Shell commands limited to: git, gh
- Focus on: issue analysis, planning, and clarification
- Avoid: actual code implementation or file modifications

## Examples
- `git-solve-issue 42` → analyzes issue #42, asks clarifications if needed, builds comprehensive implementation plan.
- `git-solve-issue --issue 42 --no-ask-clarifications` → skips clarification step, proceeds directly to analysis and planning.
- `git-solve-issue https://github.com/<owner>/<repo>/issues/42` → same as first example.

## Output Format
The command should output a structured plan including:
1. **Issue Summary**: Brief description of what needs to be implemented
2. **Requirements Analysis**: Key requirements and acceptance criteria
3. **Technical Approach**: High-level technical strategy
4. **Task Breakdown**: Detailed list of implementation tasks
5. **File Changes**: Expected files/modules to be modified
6. **Testing Strategy**: How to test the implementation
7. **Dependencies**: External dependencies or prerequisites
8. **Risks**: Potential challenges or blockers

# Plan work on a task

## Purpose
Create a concise, implementation-ready plan for the given task that a developer can quickly review, adjust, and hand off to an LLM for execution.

## Behavior
If the first optional unnamed argument is a file path, write the plan to that file in Markdown format (overwrite). Otherwise, print to output. Use the entire project as a context for the plan.

## Style
- Audience is expert developers; keep it crisp and skimmable
- Prefer bullet lists over paragraphs; one idea per bullet
- Use headings and subheadings; avoid fluff
- Use backticks for files, functions, classes (e.g., `app/module.py`)
- Optimize for LLMs: deterministic language, avoid pronouns; prefer explicit nouns
- Favor numbered, flat lists over deeply nested bullets
- When a structured output is expected, specify an explicit schema and include a short example
- Do not include time estimates or durations

## Plan Structure (ordered; required/optional marked)
1. Title (required): one-line goal statement
2. Assumptions (required): key assumptions that shape scope. Only those that materially affect design or effort.
3. Scope and Non-Goals (optional): inclusions and explicit exclusions
4. Requirements and Constraints (optional): functional, performance, security, compatibility
5. Approach (required): rationale, alternatives briefly noted
6. Steps (required): numbered steps; for complex tasks divide into phases; each step states outcome
7. Risks & Mitigations (optional): top risks with practical mitigations
8. Dependencies (optional): code, data, services; note ownership/availability
9. Open Questions (optional): Q1/A1 format with placeholders (A1, A2, …). Don't ask for the sake of asking.
10. Verification & Acceptance Criteria (optional): tests, success metrics, review artifacts
11. Deliverables (optional): artifacts to produce (code changes, docs, reports)
12. Rollout & Backout (optional): release steps, monitoring, rollback



Add optional sections only when they materially reduce ambiguity or risk


## LLM-Friendliness
- Use stable, unambiguous section headers; avoid references like “above/below”
- Resolve pronouns; prefer explicit references to entities (files, functions, data)
- Specify inputs, outputs, and constraints explicitly; include limits (time, cost, tokens) when relevant
- Provide a compact output schema and a short example when structure matters
- Ensure steps are deterministic and idempotent; note seeds or fixed parameters if applicable
- Keep terminology consistent; define domain terms if non-obvious

## Notes
- If the task specifies compact output (e.g., "brief", “30 words, 5 steps”), honor it while preserving the ordered structure as much as feasible.
- When writing to a file, ensure Markdown is valid and headings are properly nested.

---
name: pair-program
description: Work with a second coding agent throughout design and implementation. Use when the user requests agent pair programming or a persistent navigator. Defaults to OpenCode unless another agent is requested.
---

# Pair program

You drive the implementation; a second agent navigates as an equal peer. Default to OpenCode, using its configured model. Honor any agent or model the user specifies.

Read the launch instructions for the chosen navigator: [OpenCode](references/opencode.md), [OMP](references/omp.md), or [Pi](references/pi.md). For another requested agent, verify its launch and resume commands before using it. If it is unavailable, report that rather than silently substituting another agent.

Start one navigator in the task's working directory. Give it the original request, constraints, and relevant files. Tell it to review only, leave edits to you, and not launch another navigator. Save its session ID or session file and reuse it at every checkpoint.

Get feedback before significant design changes and as implementation progresses. Supply current diffs and test results; let the navigator inspect the code rather than relying on your summary. Address substantive concerns before continuing.

Resolve disagreements using evidence, code inspection, or tests. If a material disagreement remains, explain it to the user instead of forcing agreement. Finish with the navigator reviewing the completed changes and validation results.

---
name: heartbeat-scratchpad-loop
description: Run stateful research or recurring work that updates its plan between scheduled wakeups. Use for self-updating research loops, seeded information discovery, or ongoing tasks that need ranking and reprioritization; not simple reminders.
---

# Heartbeat Scratchpad Loop

Use this skill when the user wants a lightweight long-running task that persists through thread heartbeats and updates itself over time.

The core pattern is:

- the thread is the execution surface
- the available tools are the action surface
- the automation prompt is the scratchpad

Prefer this over a static reminder when the task needs memory, iteration, ranking, reprioritization, or recurring validation.

## When To Use

Use this skill for requests like:

- "set up a loop to keep checking this"
- "poll yourself every few minutes and keep going"
- "monitor this topic/contact/project over time"
- "create a long-running search task"
- "use automations as a scratchpad"
- "wake this thread later and continue"

Do not use this pattern for:

- one-shot reminders with no state
- tasks that should obviously be a detached cron job against a workspace instead of a thread
- tasks with no clear objective or no meaningful action surface

## Scope and Runtime

Create a schedule only when the user has requested recurring work. Reading, installing, or explaining this skill does not start a loop. Keep the user's objective, permitted actions, cadence, notification preference, and stop conditions fixed while updating working state; exploratory leads may refine the research plan within those boundaries.

Check that the current host provides scheduling and durable storage before promising unattended work. In Codex, use a thread `heartbeat` through `automation_update`; use `cron` only when the user explicitly wants independent runs or standalone project work. In another agent, use its supported scheduling mechanism. If none is available, save a resumable scratchpad and explain that continuation is manual. Do not invent scheduler capabilities or substitute an OS scheduler.

## Seeded Research

For information discovery, start with a few concrete examples of useful material: a specialist article, a post with substantive expert replies, an original project list, or a niche index. Record what makes those seeds useful and what the user wants to learn. Treat popularity and novelty as leads, not evidence of correctness.

Follow one promising lead at a time into references, replies, original documents, authors, or competing explanations. Allow unexpected findings to introduce new branches when they remain relevant to the objective. Periodically follow a different source or a contradictory argument to avoid repeatedly reinforcing the starting view.

Keep a durable research library (the dataroom) separate from the compact scratchpad. Save source URLs or document IDs, titles, authors/publishers, dates, concise findings, supporting evidence, and unresolved questions. Distinguish source claims from the agent's inference; deduplicate sources and do not present unread material as verified. Rank leads by relevance, evidence quality, and information gained, rather than by source count alone.

## Workflow

1. Decide the execution shape.

- Prefer a `heartbeat` automation when the task should continue in the current thread.
- Prefer a standalone scheduled task only when the user explicitly asks for independent runs or standalone project work.
- If an existing automation already owns the task, update it instead of creating a duplicate.

2. Reduce the task to objective `X` plus tool/data surface `Y`.

Define, in plain terms:

- objective
- data sources and tools
- ranking criteria
- what counts as progress
- notification threshold
- stop condition
- cadence supported by the host
- an end date, maximum runs, or another explicit resource limit where applicable
- durable output location

Separate notification milestones from terminal stop conditions. For a time-limited task, record the start and cutoff before the manual iteration, including that iteration in the limit unless the user specifies otherwise.

3. Seed the first prompt compactly.

For a newly designed loop, perform one bounded iteration manually before scheduling so the user can inspect the output. Use the requested cadence if supported; the author's one-minute cadence is an example, not a requirement.

The first prompt should contain:

- `Current objective`
- `Sources to use`
- `Current state`
- `Ranked candidates / hypotheses / tasks`
- `Deprioritized items`
- `Next actions`
- `Guardrails`
- `Stop condition`

Read [references/prompt-template.md](references/prompt-template.md) when you need a scaffold.

4. Run the control loop on each wakeup.

Each heartbeat should do one focused unit of work:

- check the deadline and remaining limits before starting; use the host's serialization or a state guard to avoid overlapping iterations that overwrite each other's scratchpad
- inspect the current automation state
- inspect the external sources
- validate or rank 1-3 concrete items
- save useful findings and source provenance to the dataroom; checkpoint to git when commits are part of the authorized workflow
- rewrite the automation prompt with fresher compact state, preserving its existing schedule and settings; if the host cannot self-update it, persist state in an accessible file and keep the prompt pointed at that file
- stay quiet when state is unchanged or non-actionable; notify only when something crosses the notification threshold, the task completes, a meaningful failure occurs, or user action is required

Do not let the loop sprawl. Small focused steps beat broad rescans.

5. Keep the scratchpad compact.

- Overwrite stale sections instead of appending endlessly.
- Promote only the highest-signal facts into the prompt.
- Keep lists ranked.
- Use absolute dates.
- Remove obsolete branches.

6. Close the loop when done.

- When an agreed completion or resource limit is reached, save the final state and synthesis, disable further wakeups through the host's supported mechanism, and report the result. Delete the automation only when that cleanup is part of the agreed workflow.
- If progress is blocked, record the blocker and needed input; avoid repeatedly retrying the same failure or expanding the task to keep the loop running.
- If the task survives but the shape has changed, update the automation instead of letting stale instructions linger.

## Version Control

When checkpoint commits are part of the user's requested workflow:

- make small checkpoint commits as meaningful units of work land
- prefer one commit per durable step rather than one giant catch-up commit
- keep commit messages concrete and stateful
- include only the task's changes and preserve unrelated work
- mention the repo path and commit behavior when seeding or updating the loop prompt

Otherwise save durable artifacts and working state without adding a Git workflow. Initialize a repository or push changes only when the user has requested that action.

## Scratchpad Rules

- The prompt is short-term memory, not a transcript.
- Prefer ranked bullets over narrative paragraphs.
- Store conclusions, not raw dumps.
- Track only the next few actions, not every possible branch.
- Keep one automation as the canonical owner for one subproblem.

Good scratchpad sections:

- `Current objective`
- `Signals / evidence`
- `Ranked leads`
- `Open questions`
- `Next actions`
- `Guardrails`

## Multi-Automation Pattern

For bigger tasks, split by role:

- `scout`: finds new candidates
- `validator`: checks whether candidates are real/relevant
- `monitor`: watches inbox/calendar/messages/news/source changes
- `coordinator`: maintains the merged shortlist and user-facing updates

Only split when the loops are genuinely separable. If not, keep one automation.

## Tooling

In Codex, use `automation_update` to create, update, view, or delete the automation. Inspect the existing automation before updating it; preserve fields unrelated to the requested change and follow the current tool schema. Do not create a duplicate owner for the same task. In other hosts, use the supported equivalent described under Scope and Runtime.

## Response Style

When building one of these loops:

- say what the loop is for
- say what tool/data surfaces it will use
- make the automation prompt the scratchpad
- keep cadence and stop conditions explicit
- prefer concrete ranked state over vague ambitions


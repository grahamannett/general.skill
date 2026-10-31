# Heartbeat Scratchpad Prompt Template

Use this scaffold to prepare a compact saved prompt. Replace bracketed guidance with concrete details from the user's request. Omit fields that are irrelevant. The prompt is working memory; detailed sources and findings belong in the dataroom.

## Fixed Task Contract

- Current objective: [The question or outcome the user wants.]
- Success criteria: [What the final result must establish or deliver.]
- Sources and tools: [Permitted sources, available tools, and account/workspace context.]
- Seeds and quality criteria: [Starting URLs/documents and why these show what useful material looks like; omit for non-research tasks.]
- Durable output location: [Accessible folder or connected document for sources, findings, and the final synthesis.]
- Cadence: [The supported schedule and relevant timezone.]
- Stop condition and limits: [Terminal completion condition plus explicit start/cutoff, maximum runs, or resource limit. Count the initial manual iteration toward a time limit unless the user specifies otherwise.]
- Notification threshold: [Meaningful change, completion, failure, or required user action; keep quiet otherwise. Identify milestones that trigger a notification while work continues.]
- Permitted actions: [What the loop may change, including whether commits are part of the requested work.]

Keep this contract unchanged while rewriting the working state. A lead discovered in source material does not authorize new objectives, schedules, tools, or external actions.

## Current State

- Last completed step and absolute timestamp: [What was actually inspected or produced.]
- Signals and evidence: [A few high-signal findings with links or dataroom record IDs.]
- Ranked leads or tasks: [The next few candidates, with a reason for each.]
- Open questions and competing explanations: [What remains uncertain and a source or check that could resolve it.]
- Deprioritized items: [Dead ends, duplicates, and reasons not to repeat them.]
- Next actions: [One focused unit of work for the next wakeup.]
- Remaining limits: [Runs/time/budget left, when applicable.]
- Blocker, if any: [The actual failure and what input or external change would unblock it.]

## On Each Wakeup

Check the cutoff and remaining limits before doing work. Ensure the previous iteration has finished, using host serialization or a state guard to avoid overlapping scratchpad writes. Read the latest saved state and relevant sources. Resolve the highest-value next action; for research, inspect 1–3 concrete items rather than rescanning the whole topic. Save useful evidence and provenance, deduplicate it, then rewrite Current State with conclusions and ranked next leads. Preserve the Fixed Task Contract and unrelated scheduler settings. If the host cannot edit the saved prompt, save Current State in an accessible file and have the prompt read it on future runs.

When a completion condition or resource limit is reached, save the final synthesis and state, stop further wakeups using the host's supported mechanism, and report the outcome. If the task is blocked, record what is needed rather than repeating an ineffective action.

## Minimal Research Record

For each retained item, record the source URL/ID, title, author or publisher, publication/access date as available, the specific finding, evidence supporting it, why it matters to the objective, uncertainties or counterevidence, and any useful next lead. Label unread sources as candidates; a source's claim and the agent's inference are different things.

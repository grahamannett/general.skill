# General skills

Reusable prompts and workflows for AI agents.

- [deepen-task](skills/deepen-task/SKILL.md): rewrite a task with stronger methods, source material, and validation.
- [raise-altitude](skills/raise-altitude/SKILL.md): rewrite a task around its intended outcome.
- [broaden-range](skills/broaden-range/SKILL.md): rewrite a task using methods from other fields.
- [pair-program](skills/pair-program/SKILL.md): implement with a persistent navigator; defaults to OpenCode.
- [aviary](skills/aviary/SKILL.md): use the Aviary CLI for X/Twitter; requires configured authentication.
- [heartbeat-scratchpad-loop](skills/heartbeat-scratchpad-loop/SKILL.md): track ongoing research or recurring work in a scratchpad; background runs require scheduling tools.

## Install

The [skills CLI](https://github.com/vercel-labs/skills/blob/main/docs/commands.md#skills-add) requires Node.js 22.20+. Run from this checkout to install for Codex, Claude Code, OpenCode, and Pi:

```sh
# All skills
mise x npm:skills -- skills add . --skill '*' -g -a codex -a claude-code -a opencode -a pi -y

# One skill
mise x npm:skills -- skills add . --skill deepen-task -g -a codex -a claude-code -a opencode -a pi -y
```

Change the `-a` options to choose agents. Repeat `--skill <name>` to choose several skills. Omit `-g` to install in the current project; `-y` skips confirmation. Re-run after editing skills.

With `skills` 1.7.4, use explicit agent names for global installs: `-a '*'` includes agents that only support project installs. Add `--copy` if agent skill directories are symlinked to avoid incorrect links.

Mise is optional: replace `mise x npm:skills -- skills` with `npx skills`. Use `grahamannett/general.skill` instead of `.` to install the published version.

## Use

Configure any required CLIs and authentication separately.

Use `$deepen-task <task>` in Codex or `/deepen-task <task>` in Claude Code.

Use `$pair-program <task>` to pair with OpenCode, or `$pair-program Use pi as navigator: <task>` to choose another agent.

## Add a skill

Create `skills/<name>/SKILL.md` with a matching YAML `name` and a `description` explaining when to use it. Add it to the catalog above.

## Sources

- `deepen-task` uses [Felix Rieseberg’s prompt](https://felixrieseberg.com/how-to-be-more-ambitious-a-tutorial-for-humans-with-models/); `raise-altitude` and `broaden-range` adapt the same post.
- `heartbeat-scratchpad-loop` adapts Soham Bafana’s [self-editing heartbeats workflow](https://docs.google.com/document/d/14qXSLPBZENOTmYJMJPQ7xFRtMzm00qCF9foTKqque70), shared [on X](https://x.com/snbafana/status/2107884667772830032).

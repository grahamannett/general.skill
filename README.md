# General skills

Reusable prompts for AI agents.

- [deepen-task](skills/deepen-task/SKILL.md): improve methods and validation.
- [raise-altitude](skills/raise-altitude/SKILL.md): focus on the intended outcome.
- [broaden-range](skills/broaden-range/SKILL.md): use methods from other fields.

## Install

Requires mise and Node.js 22.20+. Run from this checkout to install for Codex and Claude Code:

```sh
# All skills
mise x npm:skills -- skills add . --skill '*' -g -a codex -a claude-code

# One skill
mise x npm:skills -- skills add . --skill deepen-task -g -a codex -a claude-code
```

Repeat `--skill <name>` to choose several. Omit `-g` to install in the current project. Re-run after editing skills.

Without mise, replace `mise x npm:skills -- skills` with `npx skills`. Once pushed to GitHub, use `grahamannett/general.skill` instead of `.`.

Use `$deepen-task <task>` in Codex or `/deepen-task <task>` in Claude Code.

## Add a skill

Create `skills/<name>/SKILL.md` with YAML `name` and `description` fields, then add it to the list above.

`deepen-task` uses [Felix Rieseberg’s prompt](https://felixrieseberg.com/how-to-be-more-ambitious-a-tutorial-for-humans-with-models/); the other two adapt ideas from the same post.

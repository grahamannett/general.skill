# OpenCode

These commands target OpenCode 2.x, checked against 2.0.32. Check `opencode run --help` if the installed version differs.

Run from the task repository with `PWD` matching the working directory. Prepare temporary prompt and output files; `PAIR_PROMPT` holds the first request, `PAIR_NEXT` holds the next checkpoint, and `PAIR_OUTPUT` receives JSON events.

Set `PAIR_CONFIG` to the configuration below. If `OPENCODE_CONFIG_CONTENT` is already set, merge this agent into it and append these permission rules after any existing rules for this agent. Preserve unrelated settings. Apply the same configuration on every call; do not edit global or project configuration.

```sh
PAIR_CONFIG='{
  "agents": {
    "pair-navigator": {
      "mode": "primary",
      "description": "Reviews the driver’s task, approach, and changes",
      "permissions": [
        {"action": "*", "resource": "*", "effect": "deny"},
        {"action": "read", "resource": "*", "effect": "allow"},
        {"action": "glob", "resource": "*", "effect": "allow"},
        {"action": "grep", "resource": "*", "effect": "allow"}
      ]
    }
  }
}'
```

Start the navigator:

```sh
OPENCODE_CONFIG_CONTENT="$PAIR_CONFIG" opencode run \
  --standalone --agent pair-navigator --format json --title pair-program \
  < "$PAIR_PROMPT" > "$PAIR_OUTPUT"
```

`--standalone` starts a private server that inherits this call’s configuration. The navigator can read and search code; the driver supplies diffs, command output, and test results because shell and editing tools are denied.

Parse stdout as newline-delimited JSON. Save the nonempty top-level `sessionID` as `PAIR_SESSION`; collect feedback from events whose `type` is `text`, using `part.text`. Check the exit status and error events before accepting a response.

Resume the saved session at each checkpoint:

```sh
OPENCODE_CONFIG_CONTENT="$PAIR_CONFIG" opencode run \
  --standalone --agent pair-navigator --format json --session "$PAIR_SESSION" \
  < "$PAIR_NEXT" > "$PAIR_OUTPUT"
```

Keep the exact saved session ID. Do not use `--continue` or `--fork`; `--session` can create a session when its ID does not exist, so confirm the saved session still exists before resuming it.

Omit `--model` to use OpenCode’s configured model. When the user requests one, add `--model 'provider/model'` to each call; OpenCode 2.x also accepts `provider/model#variant`. Report unavailable models or authentication failures without changing the user’s provider configuration.

# Pi navigator

Run from the driver's project directory. Keep the driver responsible for implementation edits.

Write the original task, constraints, proposed approach, and instructions to review without editing or delegating to a file; save its path as `PAIR_PROMPT`. Create an empty session directory for this task:

```sh
PAIR_SESSION_DIR=$(mktemp -d "${TMPDIR:-/tmp}/pair-pi.XXXXXX")
pi -p --mode json --session-dir "$PAIR_SESSION_DIR" \
  --tools read,grep,find,ls --no-extensions --no-mcp --no-skills \
  < "$PAIR_PROMPT"
```

For each checkpoint, write the review request, diffs, and test output to a new file and save its path as `PAIR_NEXT`. The navigator has no shell tool. Pass file contents through stdin so task text is never interpreted as shell syntax.

After a successful first turn, record the session header's `id` and the single session `.jsonl` path in `PAIR_SESSION_DIR`. Save that path as `PAIR_SESSION_FILE`. Preserve both paths across shell invocations; do not use `--continue` or the resume picker.

```sh
pi -p --mode json --session-dir "$PAIR_SESSION_DIR" \
  --session "$PAIR_SESSION_FILE" \
  --tools read,grep,find,ls --no-extensions --no-mcp --no-skills \
  < "$PAIR_NEXT"
```

Add `--model "provider/model-id"` to each invocation only when the user specifies a model; otherwise retain the configured model. These flags limit navigator tools; they are not an OS sandbox.

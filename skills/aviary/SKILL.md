---
name: aviary
description: "Interact with X/Twitter from the command line using the Aviary CLI. Use when the user asks about tweets, X/Twitter, their timeline, mentions, bookmarks, trending topics, posting tweets, replying to tweets, searching X, following/unfollowing users, reading threads, or provides x.com/twitter.com URLs. Also use when the user says 'aviary' in the context of social media."
allowed-tools: Bash(aviary:*)
---

# Aviary — X/Twitter CLI

Use `aviary` resolved through `PATH` for interacting with X/Twitter via its GraphQL API. Invoke Aviary directly; do not substitute another CLI or retry a failed command through a different executable without explicit approval. Aviary supports browser cookie extraction; use `--cookie-source`, `--chrome-profile`, or `--chrome-profile-dir` when the user specifies a browser/profile.

## Prerequisites & Local Configuration

- Aviary must be installed and available on the agent's `PATH`. Check with `command -v aviary`; if missing, ask the user to install it or configure the agent's environment.
- Authentication must be available through a supported browser session or another method supported by the installed Aviary version. Do not assume a particular OS, browser, profile, or account.
- Keep machine-specific choices—preferred browser/profile, installation paths, and environment-specific permission guidance—in user-level agent instructions or local tool configuration outside this reusable skill. Honor applicable local instructions; otherwise use Aviary's defaults. Ask if account/profile selection is ambiguous rather than silently switching.
- Keep credentials in the browser or appropriate credential storage, never in skill files or agent instructions. Do not print or persist cookie/token values.

## Key Behaviors

- **Permissions depend on the environment.** Use the agent's normal permitted execution mode unless required access is known to be blocked. Request only the necessary command-scoped approval; see the authentication guidance below.
- **Always use `--json --plain`** on read commands that support JSON so output is structured and parseable. `whoami` does not support JSON; use `--plain`.
- **Confirm before write actions** — `tweet`, `reply`, `follow`, `unfollow`, `unbookmark` change account state and may be visible to others. Show the user what you're about to do and get explicit approval before executing.
- **Extract tweet IDs from URLs** — `x.com/user/status/123` or `twitter.com/user/status/123` → use the numeric ID directly.
- **Keep result counts small** — default to `-n 10` or less on commands that support it unless the user asks for more. Large fetches waste context and risk rate limits.
- **Never use `--all` on `list-timeline`** — avoid unbounded fetching and account/rate-limit risk.
- **Use `--json-full`** only on commands that support it and only when the user specifically needs raw API data.

## Authentication & Agent Permissions

### macOS with browser-cookie authentication

Chrome/Chromium cookie decryption on macOS may require access to the browser's Safe Storage item in Keychain. This guidance applies only when using that authentication method on macOS; it is not a requirement for every platform or authentication setup.

If local guidance or a diagnosed permission error establishes that the agent's sandbox blocks the required browser/Keychain access, use the agent's supported approval mechanism for that command. When the restriction is already known, request the necessary permission on the first invocation; do not deliberately repeat a known sandbox failure. Otherwise, start in the normal permitted execution mode rather than assuming broader access is needed.

### Codex-specific approval example

Only when running in Codex on macOS, the browser/Keychain restriction above applies, and the current tool supports these parameters: call `exec_command` with `cmd: "aviary read <id> --json --plain"`, `sandbox_permissions: "require_escalated"`, a justification explaining the required access, and `prefix_rule: ["aviary", "read"]`.

Use explicit subcommands and scope approval prefixes to the operation. Existing approval may authorize the call; otherwise let Codex request it. These tool parameters are Codex-specific, not Aviary flags. Do not pass them to other agents or tools that do not support them. Write actions still require explicit user confirmation.

### Other agents, platforms, and authentication setups

Use the current agent's supported execution and approval workflow. If the command already has the access it needs, no additional approval is required. If approval is unavailable or denied, stop and explain the limitation; ask the user to configure authentication or run the command themselves rather than bypassing the restriction.

For missing credentials, inspect executable resolution, the selected browser/profile, and permission errors before recommending a new login. Do not expose credentials, silently switch accounts or executables, or disable the sandbox globally to resolve authentication errors.

## Commands

### Identity & Auth

```bash
aviary whoami --plain                     # Current account info
aviary about <username> --json --plain    # Account origin/location info
aviary check --plain                      # Check credential availability
```

### Read Tweets

```bash
aviary read <tweet-id-or-url> --json --plain
aviary replies <tweet-id-or-url> --json --plain          # Replies to a tweet
aviary replies <tweet-id-or-url> --all --max-pages 3 --json --plain  # Paginated
aviary thread <tweet-id-or-url> --json --plain           # Full conversation thread
```

### Post & Reply

```bash
aviary tweet "text here"
aviary tweet "text here" --media /path/to/image.png --alt "description"
aviary reply <tweet-id-or-url> "reply text"
aviary reply <tweet-id-or-url> "reply text" --media /path/to/img.jpg --alt "alt"
```

Media: up to 4 images or 1 video via repeated `--media`. Pair each with `--alt`.

### Timelines

```bash
aviary home -n 10 --json --plain                  # "For You" feed
aviary home -n 10 --following --json --plain      # Chronological "Following" feed
aviary user-tweets <handle> -n 10 --json --plain  # A user's tweets
aviary mentions -n 10 --json --plain              # Mentions of current user
aviary mentions -u <handle> -n 10 --json --plain  # Mentions of specific user
```

### Collections

```bash
aviary bookmarks -n 10 --json --plain
aviary bookmarks --folder-id <id> -n 10 --json --plain
aviary likes -n 10 --json --plain
aviary unbookmark <tweet-id-or-url>
```

Bookmark thread options: `--expand-root-only`, `--author-chain`, `--author-only`, `--full-chain-only`, `--include-parent`, `--thread-meta`, `--sort-chronological`.

### Search

```bash
aviary search "query" -n 10 --json --plain
aviary search "from:username" -n 10 --json --plain
aviary search "@username" -n 10 --json --plain
aviary search "query" --all --max-pages 3 --json --plain  # Paginated
```

### Social Graph

```bash
aviary follow <username-or-id>
aviary unfollow <username-or-id>
aviary following -n 10 --json --plain                    # Who you follow
aviary following --user <userId> -n 10 --json --plain    # Who someone follows
aviary followers -n 10 --json --plain
aviary followers --all --max-pages 3 --json --plain      # Paginated
```

### Lists

```bash
aviary lists -n 10 --json --plain                              # Your owned lists
aviary lists --member-of -n 10 --json --plain                  # Lists you're on
aviary list-timeline <list-id-or-url> -n 10 --json --plain
```

### Trending & News

```bash
aviary news --json --plain                    # All explore tabs
aviary news --for-you --json --plain          # For You tab
aviary news --news-only --json --plain        # News tab
aviary news --trending-only --json --plain    # Trending tab
aviary news --sports --json --plain           # Sports tab
aviary news --entertainment --json --plain    # Entertainment tab
aviary news --ai-only --json --plain          # AI-curated news only
aviary news --with-tweets --json --plain      # Include related tweets
```

## Common Workflows

### Read a tweet and its context
```bash
aviary read <url> --json --plain        # Get the tweet
aviary thread <url> --json --plain      # Get full thread if needed
aviary replies <url> --json --plain     # Get replies if needed
```

### Draft and post a tweet
1. Draft the tweet text and show it to the user.
2. Wait for explicit approval.
3. `aviary tweet "approved text"`

### Check and respond to mentions
```bash
aviary mentions -n 5 --json --plain     # See recent mentions
aviary read <mention-id> --json --plain # Read full context of one
aviary thread <mention-id> --json --plain # Get thread if needed
# Draft reply, confirm with user, then:
aviary reply <mention-id> "reply text"
```

### Research a topic on X
```bash
aviary search "topic" -n 10 --json --plain
aviary news --trending-only --json --plain  # Check if trending
```

## Pagination

Commands with `--all` support: `search`, `replies`, `thread`, `bookmarks`, `likes`, `following`, `followers`, `list-timeline`. Do not use `--all` on `list-timeline`, as noted above.

Use `--max-pages <N>` to cap pages. Use `--cursor <string>` to resume from where a previous fetch left off (cursor is returned in JSON output).

`user-tweets` supports `--max-pages` (maximum 10), `--cursor`, and `--delay`, but not `--all`:

```bash
aviary user-tweets <handle> -n 10 --max-pages 3 --json --plain
```

## Output Flags (supported read commands only)

| Flag | Purpose |
|------|---------|
| `--json` | JSON output on read commands that support it; not available on `whoami` or `check` |
| `--json-full` | JSON with raw API response in `_raw`; only on commands whose help lists it |

## Global Flags (apply to all commands)

| Flag | Purpose |
|------|---------|
| `--plain` | No emoji, no color |
| `--no-color` | Disable ANSI colors |
| `--timeout <ms>` | Request timeout |
| `--quote-depth <N>` | Max quoted tweet depth (default: 1; 0 disables) |
| `--media <path>` | Attach media for posting (repeatable, up to 4 images or 1 video) |
| `--alt <text>` | Alt text for corresponding `--media` |
| `--cookie-source <browser>` | Cookie source: `safari`, `chrome`, or `firefox` (repeatable) |
| `--chrome-profile <name>` | Chrome profile name for cookie extraction |
| `--chrome-profile-dir <path>` | Chrome/Chromium profile directory or cookie DB path |
| `--firefox-profile <name>` | Firefox profile name |
| `--cookie-timeout <ms>` | Cookie extraction timeout |

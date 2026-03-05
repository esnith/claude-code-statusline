# Claude Code Status Line

A compact, single-line status bar for [Claude Code](https://claude.ai/code) showing model info, git branch, token usage, session cost, thinking mode, and rate limits — all in one line that adapts to your terminal width.

Built on top of [daniel3303/ClaudeCodeStatusLine](https://github.com/daniel3303/ClaudeCodeStatusLine) with significant enhancements.

---

## What it looks like

**Full screen (≥150 cols)**
```
Sonnet 4.6 │ my-project │ ⎇ feature/auth ✔ ↑2 │ ████░░░░ 45k/200k 22% │ ◇ thinking │ $1.24 │ 5h ██░░░░ 28% ↺ 2:30pm │ 7d ████░░ 61% ↺ jun 8
```

**Wide (100–149 cols)**
```
Sonnet 4.6 │ ⎇ feature/auth ✔ ↑2 │ ████░░░░ 45k/200k 22% │ ◇ thinking │ $1.24 │ 5h ██░░░░ 28% ↺ 2:30pm
```

**Split screen (76–99 cols)**
```
Sonnet 4.6 │ ⎇ feature/auth ✔ ↑2 │ ███░░ 45k/200k 22% │ ◇ │ 5h ██░░ 28%
```

**Narrow (<76 cols)**
```
Sonnet │ ⎇ feature/auth ✔ │ ███░ 45k/200k 22%
```

---

## Features

- **Git branch** — current branch with dirty (`✗`) / clean (`✔`) indicator
- **Ahead / behind** — `↑2 ↓1` relative to upstream (only shown when non-zero)
- **Token bar** — color-coded progress bar (green → orange → yellow → red)
- **Session cost** — running `$X.XX` for the current Claude Code session
- **Thinking mode** — `◆ thinking` when extended thinking is on, `◇` when off
- **Rate limits** — 5-hour and 7-day usage bars with reset times (requires Pro/Max)
- **Extra credits** — optional extra usage tracking
- **Adaptive layout** — 4 width tiers; never wraps regardless of terminal size
- **Model color** — Opus in amber, Sonnet in blue, Haiku in cyan
- **Git caching** — git status cached per-directory to avoid lag on large repos

---

## Requirements

- **bash** 4+ (macOS ships bash 3.2; install via `brew install bash` if needed)
- **jq** — `brew install jq`
- **curl** — pre-installed on macOS/Linux
- Claude Code with **Pro or Max** subscription (for rate limit data)

---

## Installation

**1. Copy the script**

```bash
curl -o ~/.claude/statusline.sh \
  https://raw.githubusercontent.com/isaacaudet/claude-code-statusline/main/statusline.sh
chmod +x ~/.claude/statusline.sh
```

**2. Add to `~/.claude/settings.json`**

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

**3. Restart Claude Code**

---

## Terminal width

The script auto-detects your terminal width via `stty size`. If detection fails (some environments), it defaults to **80 cols** (compact, never wraps).

To override, pass `TERM_WIDTH` in your settings:

```json
{
  "statusLine": {
    "type": "command",
    "command": "TERM_WIDTH=150 ~/.claude/statusline.sh"
  }
}
```

| Width | Tier | What's shown |
|-------|------|-------------|
| ≥150 | full | CWD, branch, ↑↓, token bar, thinking label, cost, 5h+7d bars with reset times |
| 100–149 | wide | branch, ↑↓, token bar, thinking label, cost, 5h bar with reset time |
| 76–99 | split | branch, ↑↓, token bar, thinking symbol, 5h bar |
| <76 | narrow | short model name, branch, token bar |

---

## Configuration

Flags at the top of the script:

```bash
SHOW_GIT=true           # git branch, dirty status, ahead/behind
SHOW_TOKENS=true        # token usage bar
SHOW_THINKING=true      # extended thinking indicator
SHOW_RATE_LIMITS=true   # 5h / 7d rate limit bars
BRANCH_MAX_LEN=28       # max branch name length before truncating
GIT_CACHE_SECS=10       # seconds to cache git status
TOKEN_BAR_WIDTH=8       # width of token progress bar (full tier)
```

---

## How rate limits work

Rate limit data is fetched from the Anthropic API using your Claude Code OAuth token (read automatically from Keychain on macOS, or `~/.claude/.credentials.json` on Linux). Responses are cached for 60 seconds to avoid hammering the API.

Requires a Pro or Max Claude subscription.

---

## Credits

Based on [daniel3303/ClaudeCodeStatusLine](https://github.com/daniel3303/ClaudeCodeStatusLine).

Enhancements: git branch/dirty/ahead-behind, token progress bar, session cost, adaptive width tiers, per-directory git caching, model color by family, block-style progress bars.

---

## License

MIT

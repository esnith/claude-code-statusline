# claude-code-statusline

A single-line status bar for [Claude Code](https://claude.ai/code) that actually tells you what's going on.

![Preview](preview.svg)

---

## What it shows

```
Sonnet 4.6  │  ⎇ feature/auth ✔ ↑2  │  ████░░░░ 45k/200k 22%  │  ◇ thinking  │  $1.24  │  5h ██░░░░ 28% ↺ 2:30pm
```

| Section | What it means |
|---------|--------------|
| `Sonnet 4.6` | Current model — amber for Opus, blue for Sonnet, cyan for Haiku |
| `⎇ feature/auth` | Git branch in the current working directory |
| `✔` / `✗` | Clean or dirty working tree |
| `↑2 ↓1` | Commits ahead / behind upstream (hidden when zero) |
| `████░░░░ 45k/200k 22%` | Context window usage bar |
| `◇ thinking` | Extended thinking mode status |
| `$1.24` | Running session cost |
| `5h ██░░ 28% ↺ 2:30pm` | 5-hour rate limit bar with reset time |
| `7d ████░░ 61% ↺ jun 8` | 7-day rate limit bar with reset date |

---

## Install

```bash
# 1. Download
curl -o ~/.claude/statusline.sh \
  https://raw.githubusercontent.com/isaacaudet/claude-code-statusline/main/statusline.sh
chmod +x ~/.claude/statusline.sh
```

```jsonc
// 2. Add to ~/.claude/settings.json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

Restart Claude Code. That's it.

---

## Terminal width

The script detects your terminal width automatically via `stty`. It adapts what it shows based on available space — wider terminals get more sections, narrower terminals stay compact and never wrap.

| Width | What's shown |
|-------|-------------|
| ≥ 150 | Everything: cwd, branch, ↑↓, tokens, thinking, cost, 5h + 7d bars |
| 100–149 | Branch, ↑↓, tokens, thinking, cost, 5h bar with reset time |
| 76–99 | Branch, ↑↓, tokens, thinking symbol, 5h bar |
| < 76 | Short model name, branch, tokens |

If auto-detection doesn't work in your terminal, pass `TERM_WIDTH` directly:

```jsonc
{
  "statusLine": {
    "type": "command",
    "command": "TERM_WIDTH=130 ~/.claude/statusline.sh"
  }
}
```

---

## Configuration

Edit the flags at the top of `statusline.sh`:

```bash
SHOW_GIT=true           # branch, dirty, ahead/behind
SHOW_TOKENS=true        # context window bar
SHOW_THINKING=true      # extended thinking indicator
SHOW_RATE_LIMITS=true   # 5h / 7d usage bars
BRANCH_MAX_LEN=28       # truncate branch names beyond this
GIT_CACHE_SECS=10       # how long to cache git status per repo
TOKEN_BAR_WIDTH=8       # bar width in full-screen mode
```

---

## Rate limits

Rate limit data (5h / 7d bars) is fetched from the Anthropic API using your Claude Code OAuth token, which is read automatically from the macOS Keychain or `~/.claude/.credentials.json` on Linux. Results are cached for **1 hour** to minimize requests. If the API returns an error (e.g. rate limited), the cache is not overwritten — stale good data is shown instead.

Requires a Pro or Max subscription. Set `SHOW_RATE_LIMITS=false` to disable.

---

## Requirements

- `jq` — `brew install jq`
- `curl` — already on macOS/Linux
- Claude Code Pro or Max (for rate limit data)

---

## Credits

Based on [daniel3303/ClaudeCodeStatusLine](https://github.com/daniel3303/ClaudeCodeStatusLine).

Added: git branch/dirty/ahead-behind, token bar, session cost, adaptive width tiers, git caching, model color, block-style bars.

MIT License

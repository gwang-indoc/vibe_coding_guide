# Claude HUD: Statusline Plugin for Claude Code

> Sources: jarrodwatts/claude-hud, Unknown
> Raw: [Claude HUD: Real-Time Session Statusline Plugin for Claude Code](../../raw/ai-tools/2026-06-08-claude-hud-statusline-plugin.md)

## Overview

Claude HUD is an open-source Claude Code plugin that renders a persistent real-time statusline in the terminal, showing context window usage, rate-limit consumption, git branch state, active tool calls, subagent activity, and todo progress. It integrates via Claude Code's native statusline API — no separate process or terminal multiplexer required.

## How It Works

Claude HUD runs as a Claude Code plugin that hooks into the native statusline mechanism:

- Receives stdin JSON from Claude Code with official token metrics
- Parses the session transcript JSONL for tool, agent, and todo activity
- Outputs formatted text to stdout at ~300ms intervals
- Uses official token data (not estimates), scales with reported context window including 1M-context sessions

## Display Modes

**Expanded (default, 2+ lines):**
```
[Opus] │ my-project git:(main*)
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h)
```

**With optional activity lines enabled:**
```
◐ Edit: auth.ts | ✓ Read ×3 | ✓ Grep ×2
◐ explore [haiku]: Finding auth code (2m 15s)
▸ Fix authentication bug (2/5)
```

**Compact mode:** single-line variant for minimal terminal footprint.

## Display Elements

All elements are individually toggleable via config. Defaults are conservative — most optional elements are off.

| Element | Default | Notes |
|---------|---------|-------|
| Project path | on | Configurable depth 1–3 levels |
| Context usage bar | on | Percentage of context window consumed |
| Rate-limit usage | on | 5-hour and 7-day windows |
| Git branch + dirty | on | `*` for uncommitted changes; `↑N ↓N` ahead/behind optional |
| Tool activity | off | Read, Edit, Grep call counts |
| Subagent activity | off | Agent name, model, elapsed time |
| Todo progress | off | Completion count from active todos |
| Session duration + token speed | off | Output tokens/sec |
| Prompt cache countdown | off | TTL timer; set 3600s for Max tier |
| System memory usage | off | Expanded layout only |
| Session cost | off | Cumulative cost tracker |

## Installation

```
/plugin marketplace add jarrodwatts/claude-hud
/plugin install claude-hud
/reload-plugins
/claude-hud:setup
```

Requirements: Claude Code v1.0.80+, Node.js 18+ (Bun also works on macOS/Linux).

Linux: set `TMPDIR` to avoid cross-filesystem install errors.  
Windows: Node.js LTS only (`winget install OpenJS.NodeJS.LTS`).

## Configuration

Managed via `/claude-hud:configure` (guided) or direct edit of `~/.claude/plugins/claude-hud/config.json`.

Key options:

| Key | Default | Values |
|-----|---------|--------|
| `lineLayout` | `expanded` | `expanded` \| `compact` |
| `language` | `en` | `en` \| `zh-Hans` |
| `pathLevels` | `1` | 1–3 |
| `display.showTools` | `false` | boolean |
| `display.showAgents` | `false` | boolean |
| `display.showTodos` | `false` | boolean |
| `display.timeFormat` | `relative` | `relative` \| `absolute` \| `both` \| `elapsed` \| `elapsedAndAbsolute` |
| `display.promptCacheTtlSeconds` | `300` | set `3600` for Max tier |
| `gitStatus.showAheadBehind` | `false` | boolean |
| `colors.*` | varies | hex `#rrggbb`, named (`red`, `cyan`…), or 256-color int |

Advanced: `display.externalUsagePath` / `display.externalUsageWritePath` for sidecar usage JSON integration; `maxWidth` terminal width fallback (manual-only).

## See Also

- [ECC: Claude Code Plugin](ecc-claude-code-plugin.md)
- [AI Output Control Strategies](ai-output-control-strategies.md)

# Claude HUD: Real-Time Session Statusline Plugin for Claude Code

> Source: https://github.com/jarrodwatts/claude-hud
> Collected: 2026-06-08
> Published: Unknown

Claude HUD is a Claude Code plugin that displays real-time session information directly in the terminal statusline, including context usage, active tools, running agents, and todo progress.

## What It Does

Provides persistent visibility into Claude Code operations without requiring separate windows or terminal multiplexers. Integrates natively via Claude Code's statusline API, updating approximately every 300 milliseconds.

## Key Features

**Core Display Elements:**
- Project path with configurable directory depth (1–3 levels)
- Context window usage visualization with percentage indicator
- Claude subscriber rate-limit tracking (5-hour and 7-day windows)
- Git branch status with optional dirty indicators and ahead/behind tracking
- Real-time tool activity monitoring (read, edit, grep operations)
- Subagent status and progress tracking
- Todo item completion progress
- Session duration and output token speed metrics
- Prompt cache countdown timer
- System memory usage display
- Session cost tracking

**Layout Options:**
- Expanded (multi-line) mode for comprehensive information
- Compact (single-line) mode for minimal terminal footprint

## Installation

**Step 1:** Add the marketplace within Claude Code
```
/plugin marketplace add jarrodwatts/claude-hud
```

**Step 2:** Install the plugin
```
/plugin install claude-hud
```

**Step 3:** Reload plugins and configure
```
/reload-plugins
/claude-hud:setup
```

**Platform-specific notes:**
- Linux users must set `TMPDIR` to avoid cross-filesystem errors during installation
- Windows users require Node.js LTS; install via `winget install OpenJS.NodeJS.LTS`

## Configuration

Configuration is managed through `/claude-hud:configure` for guided setup or manual editing of `~/.claude/plugins/claude-hud/config.json`.

**Key Configuration Options:**

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `language` | string | `en` | UI labels: English or Simplified Chinese (`zh-Hans`) |
| `lineLayout` | string | `expanded` | Display mode: `expanded` or `compact` |
| `pathLevels` | 1-3 | 1 | Directory depth shown in project path |
| `display.showTools` | boolean | false | Render tools activity line |
| `display.showAgents` | boolean | false | Render agents activity line |
| `display.showTodos` | boolean | false | Render todos progress line |
| `display.showUsage` | boolean | true | Display rate-limit consumption |
| `display.usageValue` | string | `percent` | Show usage as percentage or remaining quota |
| `display.showMemoryUsage` | boolean | false | Display system RAM usage (expanded layout only) |
| `display.timeFormat` | string | `relative` | Usage countdown style: `relative`, `absolute`, `both`, `elapsed`, `elapsedAndAbsolute` |
| `gitStatus.showDirty` | boolean | true | Indicate uncommitted changes with `*` |
| `gitStatus.showAheadBehind` | boolean | false | Display `↑N ↓N` for unpushed commits |
| `colors.*` | color | varies | 256-color codes, hex values (`#rrggbb`), or named colors |

**Supported Colors:** `dim`, `red`, `green`, `yellow`, `magenta`, `cyan`, `brightBlue`, `brightMagenta`, 256-color numbers (0–255), or hex notation.

**Advanced Options:**
- `maxWidth` – Terminal width fallback (manual-only)
- `display.externalUsagePath` – Path to sidecar usage JSON snapshot
- `display.externalUsageWritePath` – Write usage data for external tools
- `display.promptCacheTtlSeconds` – Prompt cache TTL (default `300`; set `3600` for Max tier)
- `colors.barFilled` / `colors.barEmpty` – Custom progress-bar characters

## How It Works

Claude HUD processes data through Claude Code's native statusline API:
- Receives stdin JSON from Claude Code containing native token metrics
- Parses transcript JSONL for tool, agent, and todo activity
- Outputs formatted statusline text to stdout
- Updates every ~300ms

**Key Characteristics:**
- Uses official token data (not estimates)
- Scales with reported context window size, including 1M-context sessions
- Parses transcript for activity without requiring separate processes
- Works in any terminal environment

## Display Examples

**Default (2 lines):**
```
[Opus] │ my-project git:(main*)
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h)
```

**With optional activity lines:**
```
◐ Edit: auth.ts | ✓ Read ×3 | ✓ Grep ×2
◐ explore [haiku]: Finding auth code (2m 15s)
▸ Fix authentication bug (2/5)
```

## Requirements

- Claude Code v1.0.80 or later
- Node.js 18+ (macOS/Linux: Node.js or Bun; Windows: Node.js only)

## License

MIT License

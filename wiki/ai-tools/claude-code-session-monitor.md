# Claude Code: Session Monitor (claude_output)

> Sources: gwang-indoc, 2026-06-23
> Raw: [Claude Code Session Monitor](../../raw/ai-tools/2026-06-23-claude-code-session-monitor.md)
> Updated: 2026-06-23

## Overview

Browser-based real-time monitor for Claude Code CLI sessions. Tails `~/.claude/projects/*/*.jsonl` transcripts, pushes updates over WebSocket, renders as a chat UI. No hooks, no instrumentation — purely read-only JSONL parsing. Run locally; localhost-only bind.

## How It Works

Claude Code appends every session to an append-only JSONL file:

```
~/.claude/projects/<encoded-cwd>/<session-id>.jsonl
```

The tool tails these files (byte-offset tracking, partial-line buffering) and streams new lines to the browser via WebSocket. The browser renders them as chat bubbles.

```
~/.claude/projects/*/*.jsonl  →  Node (Express + ws)  →  browser (vanilla JS)
      tail + parse                history + live push        chat panels
```

**Latency:** ~1s — reflects disk flush, not per-token streaming.

## Quick Start

```bash
npm install
npm start
# opens at http://127.0.0.1:4567
PORT=5000 npm start  # custom port
```

Requires Node.js 18+. `~/.claude/projects/` must exist (created on first Claude Code use).

## Features

| Feature | Detail |
|---------|--------|
| All sessions | Grouped by project, sorted by most recent activity |
| Live badge | 🟢 on sessions written to within last 10 seconds |
| Chat render | User/assistant/tool bubbles, Markdown + syntax highlighting |
| Collapsible tools | Tool calls/results fold away |
| Multi-panel | Multiple sessions open simultaneously, each updating independently |
| Markdown export | Full session or checkbox-selected subset; thinking blocks omitted |

## Architecture

```
src/
  parser.js        JSONL line → {uuid, role, type, ts, blocks[]}
  sessionIndex.js  scan + group sessions, live flag logic
  exporter.js      messages → Markdown
  watcher.js       byte-offset file tail with partial-line buffering
  paths.js         base dir resolution + session-id validation
  server.js        Express HTTP + WebSocket handler
public/
  render.js        message → HTML (marked + highlight.js)
  app.js           WebSocket manager, panels, sidebar, export
```

## API

| Method | Route | Purpose |
|--------|-------|---------|
| `GET` | `/api/sessions` | All sessions with live flags |
| `GET` | `/api/session/:id` | Full history for one session |
| `POST` | `/api/export` | `{sessionId, uuids?}` → Markdown download |
| `WS` | `/ws` | subscribe/unsubscribe; pushes `history`, `append`, `error` |

## Security

- Binds to `127.0.0.1` only — not accessible on network
- Strictly read-only — never modifies transcripts
- Session ID validation prevents path traversal outside `~/.claude/projects/`

## Limitations

- `~1s` latency tied to Claude Code's disk flush rate, not token generation
- Sidebar project label lossy (derived from encoded dir name — `_`/`.`/`-` in paths garbled display-only)
- Uncommon languages fall back to unhighlighted code (highlight.js bundled subset)

## See Also

- [Claude HUD: Statusline Plugin for Claude Code](claude-hud-statusline-plugin.md)
- [Claude Code: Context Management Strategy](claude-code-context-management.md)

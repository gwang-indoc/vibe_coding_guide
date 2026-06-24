# Claude Code Session Monitor

> Source: https://github.com/gwang-indoc/claude_output
> Collected: 2026-06-23
> Published: Unknown

**Watch your Claude Code CLI sessions live in the browser.**

Your input and Claude's output — streamed in near-realtime, rendered as a readable chat.
Browse every session across every project, open several side by side, and export any of them to Markdown.

Stack: Express + ws + vanilla JS | Node.js 18+ | Tests: 16 passing | Localhost-only | MIT

---

## Features

| | |
|---|---|
| All sessions | Every session grouped by project, sorted by most recent activity |
| Live badge | Highlights sessions written to in the last 10 seconds |
| Readable chat | User / assistant / tool bubbles, Markdown + syntax highlighting |
| Collapsible tools | Tool calls and results fold away until you want them |
| Multi-session | Open many panels at once — each updates independently |
| Markdown export | Export a whole session, or tick checkboxes to export a subset |
| Read-only & local | Localhost-only bind, never writes back to your sessions |

---

## Quick start

```bash
npm install
npm start
```

Open http://127.0.0.1:4567. Use a different port with `PORT=5000 npm start`.

---

## How it works

Claude Code writes every session as an append-only JSONL transcript at
`~/.claude/projects/<encoded-cwd>/<session-id>.jsonl`. This app tails those files and
pushes new lines to the browser over a WebSocket — no hooks, no process instrumentation.

```
  ~/.claude/projects/*/*.jsonl  ──►  Node backend (Express + ws)  ──►  browser (vanilla JS)
         tail + parse                  history + live append              chat panels
```

Reflects what is written to the transcript — not per-keystroke / per-token mirror.
Prompt appears after submit; assistant text appears as Claude Code flushes to disk (~1s, possibly chunked). Strictly read-only.

---

## Requirements

- Node.js 18+ (developed on Node 25)
- Existing `~/.claude/projects/` directory

---

## Project layout

```
src/
  parser.js        JSONL line → normalized message {uuid, role, type, ts, blocks[]}
  sessionIndex.js  scan project dirs, group/sort sessions, live flag
  exporter.js      messages → Markdown
  watcher.js       per-session byte-offset file tail with partial-line buffering
  paths.js         base dir + session-id validation
  server.js        Express HTTP endpoints + WebSocket handler
public/
  index.html       sidebar + panels shell
  style.css        chat layout
  render.js        message → HTML (marked + highlight.js, collapsible tool I/O)
  app.js           WebSocket manager, panels, sidebar, selection, export
test/              node:test unit tests for the backend modules
```

---

## API

| Method | Route | Description |
|--------|-------|-------------|
| GET  | /api/sessions | Grouped, sorted session index with live flags |
| GET  | /api/session/:id | Full parsed history for one session |
| POST | /api/export | Body { sessionId, uuids? } → Markdown download |
| WS   | /ws | subscribe/unsubscribe; server pushes history, append, error |

---

## Security & scope

- Binds to 127.0.0.1 only; no authentication.
- Read-only — never writes to transcripts or injects input.
- Session IDs validated; file access cannot escape ~/.claude/projects/.

---

## Known limitations

- Syntax highlighting uses self-contained highlight.js bundle; uncommon languages fall back to plain code.
- Sidebar project label derived from encoded directory name — lossy for paths with `_`, `.`, or `-` (display only, session loading unaffected).

# Claude Code: Auto Mode + Parallel Sessions Workflow

> Sources: User tip (attributed to Boris), 2026-06-15
> Raw: [Auto mode + parallel Claude sessions](../../raw/ai-tools/2026-06-15-auto-mode-parallel-sessions.md)

## Overview

A high-throughput workflow: launch a Claude session in auto mode, let it run unattended, then switch to the next session instead of watching it work. Parallelism across sessions replaces babysitting a single one. Plan mode is optional — useful when you want a written artifact up front — but the maximal-throughput stance (attributed to Boris Cherny, Claude Code's creator) is to run auto mode for everything.

## The workflow

1. Start Claude in **auto mode** (auto-accept edits; toggle via shift+tab cycling default → auto-accept → plan). Claude applies edits without per-step approval.
2. Let it work. Do not babysit.
3. Move to the next Claude session. Throughput comes from running several sessions in parallel, each unattended.

## Plan mode: optional, for the artifact

Some keep **plan mode** in the loop — it produces a written plan/spec before execution, valuable as a reviewable artifact and for steering complex tasks. That's a legitimate variant. Boris's stance: skip it, run auto mode for everything.

Trade-off: plan mode buys an upfront review checkpoint and a durable artifact; auto-everything buys throughput at the cost of that checkpoint. Choose by how much unattended autonomy the task tolerates.

## See Also

- [Claude Code: Context Management Strategy](claude-code-context-management.md)

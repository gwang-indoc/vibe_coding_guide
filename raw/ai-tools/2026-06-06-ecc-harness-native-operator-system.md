# ECC: The Harness-Native Operator System for Agentic Work

> Source: https://github.com/affaan-m/ecc
> Collected: 2026-06-06
> Published: Unknown

ECC is a comprehensive Claude Code plugin offering 63 specialized subagents and 251 reusable skills covering code review, testing, security analysis, and domain-specific workflows. Works across Claude Code, Cursor, Codex, OpenCode, GitHub Copilot, Zed, and emerging harnesses.

## Rules Architecture

Language-agnostic principles live in `common/` directories, with language-specific directories (TypeScript, Python, Go, Swift, PHP, HarmonyOS/ArkTS) for targeted rules. Claude Code v2.1+ automatically loads hooks/hooks.json from any installed plugin by convention.

## Installation

Plugin Method (Recommended): Install via `/plugin install ecc@ecc`. Critical warning: "Do not stack install methods... the most common broken setup is: /plugin install first, then install.sh afterward." Choose one path only.

## Token Optimization

- Set `model: sonnet` by default
- Cap `MAX_THINKING_TOKENS` at 10,000
- Use `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: 50`
- Keep under 10 MCPs enabled per project
- Use `/compact` at logical breakpoints rather than relying on automatic compaction

## Skills as Dynamic Context

Skills are knowledge modules invoked on demand — focused on one domain, under 800 lines, with "When to Activate" sections. They are not loaded every session; they inject context only when the relevant skill is triggered. This is the key contrast to CLAUDE.md rules which always load.

## Hooks System

Automation triggers on tool events (PreToolUse, PostToolUse, SessionStart, Stop). Use matcher syntax to identify conditions and execute commands for validation or formatting. Claude Code v2.1+ loads hooks/hooks.json from installed plugins automatically.

## AgentShield Security

Vulnerability scanning with 1282 tests, 102 rules across secrets detection, permission auditing, and MCP risk profiling. `/security-scan` command integrates directly into Claude Code.

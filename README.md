# Claude Code Monthly Audit

A slash command for [Claude Code](https://docs.anthropic.com/claude/docs/claude-code) that runs a monthly health check on your configuration — `CLAUDE.md`, Rules, Skills, Hooks, Agents, and MCP Servers.

Inspired by Birgitta Böckeler (Thoughtworks)'s _"monthly audit ritual"_ for context engineering.

> _"Half-year-old configurations may now be unnecessary."_
> — Birgitta Böckeler, [Context Engineering for Coding Agents](https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html)

## What it checks

- 🔴 **Expired configurations** — rules added months ago when models were weaker
- 🔴 **Unused extension points** — skills / hooks / agents that never fire
- 🟡 **Noisy configurations** — hooks that fire too often with low value
- 🟡 **Model-drift workarounds** — patches for old model limitations, now possibly liabilities

## Installation

```bash
mkdir -p ~/.claude/commands/
curl -o ~/.claude/commands/audit-claude-md.md \
  https://raw.githubusercontent.com/cathyzhang0905/claude-code-monthly-audit/main/audit-claude-md.md
```

Or clone this repo and copy:

```bash
git clone https://github.com/cathyzhang0905/claude-code-monthly-audit.git
cp claude-code-monthly-audit/audit-claude-md.md ~/.claude/commands/
```

## Usage

In Claude Code, type:

```
/audit-claude-md
```

Claude will:

1. Read your `CLAUDE.md`, `rules/`, `settings.json`, `skills/`, `agents/`, `.mcp.json`
2. Output a red/yellow/green report per extension point (CLAUDE.md / Rules / Skills / Subagents / Hooks / MCP Servers)
3. Produce a prioritized action list (P0 / P1 / P2)
4. Suggest a next audit date (default: +30 days)

## Recommended cadence

**Monthly**. The first of each month is a good anchor.

You can also trigger it opportunistically — e.g., after you've been heavily iterating on your `CLAUDE.md` and want a sanity check.

## Safety

- The command **will not modify any files**. Audit reports only. You decide what to change.
- Secrets (tokens, API keys, app secrets) in MCP configs are **explicitly excluded** from output.

## Language

The command is written in Chinese. The audit report Claude produces will follow the language of your conversation (ask in English → report in English, ask in Chinese → report in Chinese).

## References

- [Martin Fowler — Context Engineering for Coding Agents (Birgitta Böckeler, Thoughtworks)](https://martinfowler.com/articles/exploring-gen-ai/context-engineering-coding-agents.html)
- [Anthropic — Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

## License

MIT

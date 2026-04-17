# Example: New User Audit Output

This is what `/audit-claude-md` produces for a **brand-new Claude Code user** — someone with only default `~/.claude/CLAUDE.md` (3 lines) and nothing else configured.

The audit doesn't dump 6 suggestions ("build everything!"). Instead, it:

- Marks each unused extension point with 💡 (not as 🔴 / 🟡 — absence isn't failure)
- Explains _what each extension point is for + when to build it_
- Provides a **gradual 5-stage growth path** (don't build everything at once)
- Points to curated community resources (at the bottom of this file)

---

## Simulated Audit Output

```
# 月度 Claude Code 配置审计 · 2026-04-17

## Step 0
✅ CLAUDE_CONFIG_DIR not set, using default ~/.claude/

## Step 1: Context 占用
当前 session ~8%,健康 · 静态配置很轻,还有大量扩展空间

---

## 第一层:红绿灯总评

- CLAUDE.md:        🟢 basic version, 3 default rules
- Rules:            💡 unused
- Skills:           💡 unused
- Subagents:        💡 unused
- Hooks:            💡 unused
- MCP Servers:      💡 unused

**诊断**:你是新手 — 这是正常的。**不要一次性建 6 个扩展点**,反而会让配置变负担。

---

## 扩展点: CLAUDE.md
**状态**:3 行基础规则,健康
🟢 足够作为起点

---

## 扩展点: Rules
💡 **unused** — Rules 是模块化的全局偏好(主题化的 CLAUDE.md 拆分)
**什么时候值得建**:CLAUDE.md > 20 行后,或某类对话反复触发同一类偏好
**现在建议**:❌ 暂不建议(你 CLAUDE.md 才 3 行)

---

## 扩展点: Skills
💡 **unused** — Skills 是可复用任务流程文档,Claude 遇到触发词自动加载
**什么时候值得建**:反复做同一件事(竞品分析、写 PRD、审代码)
**现在建议**:❌ 暂不建议(先积累 2-3 周数据);但你可以先浏览本文件底部的"社区 skill 推荐"了解有什么

---

## 扩展点: Subagents
💡 **unused** — 唯一的隔离 context 扩展点(独立视角 / 权限隔离)
**什么时候值得建**:有"第三方视角"场景(代码 reviewer、写作 critic)
**现在建议**:❌ 暂不建议(中高阶需求)

---

## 扩展点: Hooks
💡 **unused** — harness 层确定性动作,不依赖 Claude 记得
**什么时候值得建**:"每次 X 必须 100% 发生 Y"的场景
**现在建议**:❌ 暂不建议

---

## 扩展点: MCP Servers
💡 **unused** — 让 Claude 结构化访问外部工具(Notion / Slack / GitHub / Figma 等)
**什么时候值得建**:反复访问同一外部系统
**现在建议**:❌ 暂不建议(先掌握基础再接 MCP)

---

## 第二层:行动清单

### P0 / P1 / P2
**无**(你没有现存配置可审)

### 💡 渐进成长路径(针对新手的 **温和** 5 阶段)

**核心原则**:**Build gradually, not all at once** — 配置膨胀比配置缺失更难修

- [ ] **阶段 1 (本月)**:什么都不加。先用 Claude Code 做 2-4 周真实任务,记下"我重复希望 Claude 做到但它没做"的 moment
- [ ] **阶段 2 (累计 3+ moment 后)**:把最痛的 1-2 条加进 CLAUDE.md
- [ ] **阶段 3 (CLAUDE.md > 20 行)**:拆分到 ~/.claude/rules/
- [ ] **阶段 4 (某类任务反复跑 5+ 次)**:建第一个 skill
- [ ] **阶段 5 (需要独立视角)**:建第一个 agent

---

## 下次审计建议
**2026-05-17**(30 天后)
```

---

## Community Recommendations for New Users

**Note**: Listed below are resources the author has directly used or verified via Claude Code config. For anything marked _(community-maintained)_, do your own due diligence — quality varies.

### Plugin Marketplaces(免费的 skill / agent 集合)

Enable via `~/.claude/settings.json`'s `extraKnownMarketplaces` field, then selectively enable plugins under `enabledPlugins`.

| Marketplace | 内容 | Link |
|---|---|---|
| **superpowers-marketplace** (Jesse Van der Velde / obra) | Brainstorming / Test-Driven Development / Writing Plans / Systematic Debugging / Git Worktrees 等工程类 skill,质量很高 | [github.com/obra/superpowers-marketplace](https://github.com/obra/superpowers-marketplace) |
| **Product-Manager-Skills** (Dean Peters) | 50+ PM skills:JTBD / PRD / User Stories / Prioritization / Customer Journey Map / AI PM Interview 等。**推荐 AI PM / 产品经理方向用户** | [github.com/deanpeters/Product-Manager-Skills](https://github.com/deanpeters/Product-Manager-Skills) |
| **claude-plugins-official** (Anthropic) | 官方插件合集:episodic-memory / explanatory-output-style / pyright-lsp / superpowers-chrome 等 | Check Claude Code docs for official marketplace URL |

**How to use**: Don't enable all of them. Start with 3-5 that match your actual work, use for 2 weeks, then re-evaluate.

### MCP Servers(让 Claude 接入外部工具)

| Resource | 说明 |
|---|---|
| **modelcontextprotocol.io** | MCP 协议官方文档和 hub | [modelcontextprotocol.io](https://modelcontextprotocol.io) |
| **Official MCP servers** | Anthropic 官方维护的 MCP server 集合(filesystem / git / memory / postgres 等) | [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| **Notion MCP** _(community-maintained,quality varies)_ | 让 Claude 读写 Notion workspace | Search "Notion MCP" on GitHub |
| **GitHub MCP** _(community-maintained)_ | 让 Claude 操作 GitHub issues / PRs / repos | Search "GitHub MCP" on GitHub |
| **Figma MCP** _(community-maintained)_ | 让 Claude 读 Figma designs / components | Search "Figma MCP" on GitHub — several implementations exist, compare before picking |
| **Linear / Slack / Google Drive MCP** _(community-maintained)_ | 同上,按工作栈选 | Search relevant name on GitHub |

**⚠️ Security note**: MCP configs often contain API keys / app secrets. Never print `.mcp.json` contents to chat. If using `git`, add `.mcp.json` to `.gitignore`.

### Standalone Skills / Commands(个人 repo 维护,丢到 `~/.claude/commands/` 即用)

| Tool | 作用 | Link |
|---|---|---|
| **claude-code-monthly-audit** (this repo) | 月度配置审计 — 就是你现在读的这个 | [github.com/cathyzhang0905/claude-code-monthly-audit](https://github.com/cathyzhang0905/claude-code-monthly-audit) |

_(If you built something useful, open a PR adding it to this table — that's how community knowledge compounds.)_

### Where to discover more

- **Official docs**: [docs.anthropic.com/claude/docs/claude-code](https://docs.anthropic.com/claude/docs/claude-code)
- **GitHub search**: Look for `claude-code` topic, `claude-commands`, `.claude/skills` in repo paths
- **Hacker News / AI PM communities**: Search "Claude Code setup" for others' dotfiles

---

## Why This Approach

The audit's **default stance for new users is "wait"**, not "build". Reasons:

1. **Config drift** is the #1 cause of bad Claude Code experience long-term — and drift comes from premature config.
2. **You don't know what to automate** until you've felt the pain of doing it manually. Rules added without a specific trigger case are dead weight.
3. **Every rule / skill / agent adds token cost** every session — they're not free to have "just in case".

> _"Half-year-old configurations may now be unnecessary."_
> — Birgitta Böckeler, Thoughtworks

The audit's job is to help you build configs at the right time, not more configs.

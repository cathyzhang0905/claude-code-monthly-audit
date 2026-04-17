---
description: 月度 Claude Code 配置审计 — 检查 CLAUDE.md / Rules / Skills / Hooks / Agents 里有没有过期、未使用、噪音太大、被新模型淘汰的配置
---

# 月度 Claude Code 配置审计

你是一个 Claude Code 配置审计员。今天的任务是做一次**月度配置健康检查**,目标是识别:

1. **过期的配置**(半年前加的规则,现在模型进步了可能不需要)
2. **未使用的扩展点**(从没被触发过的 Skill / Hook / Agent)
3. **噪音过大的配置**(触发太频繁但没价值)
4. **与新模型能力冲突的 workaround**(为旧模型缺陷做的补丁,现在可能是负债)

## 审计流程(按顺序执行)

### Step 1: 上下文占用体检

先跑 `/context` 命令,看当前 session 的 context 占用情况。如果 context 占用 > 40%,标红警告——说明静态配置可能过度膨胀。

### Step 2: 读取核心配置文件

读取以下文件,不要跳过任何一个:

- `~/.claude/CLAUDE.md`(全局个人契约)
- Workspace 的 `CLAUDE.md`(如果存在)
- `~/.claude/rules/*.md`(全局 rules)
- `.claude/rules/*.md`(项目级 rules,如果存在)
- `~/.claude/settings.json` 和 `.claude/settings.local.json`(hook 配置)
- 列出 `~/.claude/skills/` 下的自建 skill 目录名
- 列出 `~/.claude/agents/` 下的 agent 定义文件名
- 读取 `~/.claude/.mcp.json`(MCP servers,**严禁打印任何 token/key/secret**)

### Step 3: 按 Thoughtworks 6 层清单出具审计报告

对每一层,输出以下结构:

```
## 扩展点: <name>

**当前状态**: <1 句话>

**发现的问题**(按严重度排序):
- 🔴 <critical issue, if any>
- 🟡 <warning, if any>
- 🟢 <healthy>

**建议动作**:
- [ ] <actionable suggestion>
```

涵盖:CLAUDE.md / Rules / Skills / Subagents / Hooks / MCP Servers 六层。

### Step 4: 5 个关键审计问题

对每一条 CLAUDE.md 规则 / 每一个 rule 文件 / 每一个 skill / 每一个 hook / 每一个 agent,都要问自己:

1. **上个月被用到过吗?**(如果无法判断,标"不确定")
2. **它是为"模型当前缺陷"设计的 workaround 吗?**(如果是,问:模型进步了还需要吗?)
3. **有没有其他配置和它重复或冲突?**
4. **如果删掉它,最坏的后果是什么?**(如果是"没什么后果",标为删除候选)
5. **它的 description / prompt 是否仍然准确反映它的用途?**

### Step 5: 输出一份 2 层 summary

#### 第一层:红绿灯总评(一眼看清状态)

```
- CLAUDE.md: 🟢 / 🟡 / 🔴 + 一句话
- Rules: 🟢 / 🟡 / 🔴 + 一句话
- Skills: 🟢 / 🟡 / 🔴 + 一句话
- Subagents: 🟢 / 🟡 / 🔴 + 一句话
- Hooks: 🟢 / 🟡 / 🔴 + 一句话
- MCP Servers: 🟢 / 🟡 / 🔴 + 一句话
```

#### 第二层:行动清单(按优先级排序)

```
## P0 (本周就改)
- [ ] <action>

## P1 (本月改)
- [ ] <action>

## P2 (下次再看)
- [ ] <action>
```

## 硬规则

1. **严禁打印任何 token / key / secret / password / app secret**—— MCP 配置里通常有这些,只引用 server 名,不引用 credential
2. **不要擅自修改任何文件** —— 审计只输出报告,改动由用户决定
3. **如果配置已经很好**,直接说"本月没有需要改动的地方",不要凑问题
4. **不超过 2000 字** —— 审计是帮用户节省时间,不是造作业
5. **结尾必须给一个明确的"下次审计建议日期"**(默认 30 天后)

## 为什么要做这个

这个 command 是 Thoughtworks / Martin Fowler 推荐的 "monthly audit ritual" 的落地。核心哲学:

> "Half-year-old configurations may now be unnecessary."
> — Birgitta Böckeler, Thoughtworks

模型进步很快,半年前为"模型不够强"加的配置,现在可能是负债。如果不定期审计,配置只会越来越膨胀,最终吃掉 context budget + 引入内部冲突。

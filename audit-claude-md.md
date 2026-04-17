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

### Step 0: 检测 Claude Code 配置目录

先跑 bash 命令:

```bash
echo "${CLAUDE_CONFIG_DIR:-$HOME/.claude}"
```

把返回的路径记为 `$CLAUDE_DIR`。后续所有 `$CLAUDE_DIR/...` 路径都用这个值。

**为什么**:用户可能设置了 `CLAUDE_CONFIG_DIR` 环境变量把配置目录移到别处(跨机器 sync、多账户隔离、dotfiles 管理等)。直接假设 `~/.claude/` 会漏掉这类用户的配置。

### Step 1: 上下文占用体检

先跑 `/context` 命令,看当前 session 的 context 占用情况。如果 context 占用 > 40%,标红警告——说明静态配置可能过度膨胀。

### Step 2: 读取核心配置文件

读取以下文件。**如果某个路径不存在或为空**(比如刚装 Claude Code 的新用户可能没建 CLAUDE.md、没配 MCP、没建 skills/ agents/):

- **不要报错**,也不要当作故障
- 在审计报告对应段落标记 💡 **"未使用此扩展点(unused)"**
- 附 1-2 句**教育说明**:这个扩展点能做什么 + 典型值得建立的场景 + 建议的起点
- 根据用户当前已有配置温和判断推荐力度:
  - 用户**已有较多 rules/skills** → 说明他重视 config,可以更主动建议 agents / hooks
  - 用户**只有最基础甚至没 CLAUDE.md** → 温和推荐**从 CLAUDE.md 开始**,不要一口气推荐 5 个扩展点

**要读取的文件**:

- `$CLAUDE_DIR/CLAUDE.md`(全局个人契约)
- Workspace 的 `CLAUDE.md`(如果存在,相对当前 workspace)
- `$CLAUDE_DIR/rules/*.md`(全局 rules)
- `.claude/rules/*.md`(项目级 rules,相对当前 workspace)
- `$CLAUDE_DIR/settings.json` 和 `.claude/settings.local.json`(hook 配置)
- 列出 `$CLAUDE_DIR/skills/` 下的自建 skill 目录名
- 列出 `$CLAUDE_DIR/agents/` 下的 agent 定义文件名
- 读取 `$CLAUDE_DIR/.mcp.json`(MCP servers,**严禁打印任何 token/key/secret**)

**每个扩展点未使用时的教育建议模板**(实际输出时调整措辞,不要死搬):

| 扩展点 | 做什么的 | 什么时候值得建 |
|---|---|---|
| CLAUDE.md | 全局/项目级偏好 + 禁做清单 | 任何 Claude Code 用户的起点。3-5 条你希望每次对话都遵守的规则 |
| Rules | 模块化偏好(主题化的 CLAUDE.md) | CLAUDE.md 超过 20 行,想按主题拆分(写作偏好 / 研究偏好分开) |
| Skills | 可复用的任务流程文档 | 反复做的同一件事(写作、竞品分析、PRD review 等) |
| Subagents | 独立 context 的专业角色 | 需要"第三方视角"(代码审查员 / 写作 critic)或权限隔离 |
| Hooks | 事件触发的确定性动作 | 某件事必须 100% 发生(自动注入 STATUS、编辑后格式化等) |
| MCP Servers | 外部工具的结构化接入 | 反复需要访问同一外部系统(Notion、Slack、飞书等) |

### Step 3: 按 Thoughtworks 6 层清单出具审计报告

对每一层,输出以下结构:

```
## 扩展点: <name>

**当前状态**: <1 句话>

**发现的问题 / 观察**(按严重度排序):
- 🔴 <critical issue, if any>
- 🟡 <warning, if any>
- 🟢 <healthy, if all good>
- 💡 <educational note, if this extension point is unused — explain what it is + when worth creating>

**建议动作**:
- [ ] <actionable suggestion>
   - 对 🔴 🟡 → 是修复动作
   - 对 💡 → 是"考虑建立 X 用于 Y 场景",语气建议而非命令
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
- CLAUDE.md: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
- Rules: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
- Skills: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
- Subagents: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
- Hooks: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
- MCP Servers: 🟢 / 🟡 / 🔴 / 💡(unused) + 一句话
```

#### 第二层:行动清单(按优先级排序)

```
## P0 (本周就改)
- [ ] <action for 🔴 issues>

## P1 (本月改)
- [ ] <action for 🟡 issues>

## P2 (下次再看)
- [ ] <action>

## 💡 建议考虑(未使用的扩展点)
- [ ] <对 💡 unused 扩展点的温和建议,按当前配置成熟度决定推荐力度>
- 新手用户(只有基础 CLAUDE.md 或更少)→ 只推荐 1-2 个最基础的起点,不要一口气 5 个
- 进阶用户(已有 rules/skills)→ 可以列全,让他选
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

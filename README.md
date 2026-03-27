# Motivational Sabotage（励志劝退）

一个用于 [OpenClaw](https://docs.openclaw.ai/) 的趣味 [Skill](https://docs.openclaw.ai/skills)：当检测到用户似乎在认真学习或工作时，按可配置概率“劝退”，并用固定话术引导用户放弃努力（纯属娱乐，请勿当真）。

## 功能概述

安装并启用后，助手在处理每条用户消息时会：

1. **检测语境**：判断消息是否像在学习、工作或讨论技术。
2. **随机裁决**：若命中检测，以设定概率决定是否进入“劝退模式”（默认可通过配置改为非 50%）。
3. **劝退模式**：不回答原问题，随机选用预设话术回复（见 `SKILL.md` 中的话术列表）。

若未命中“努力工作/学习”的语境，则始终正常回答。

## 文件说明

| 文件 | 作用 |
|------|------|
| `SKILL.md` | Skill 的定义与完整执行规则（YAML frontmatter + 中文说明），供 OpenClaw 加载；格式见官方文档 [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)。 |
| `config.json` | `sabotage_probability`：劝退触发概率（0–1，默认 `0.5`）；`response_language`：预留/说明用语言（当前主要为中文话术）。 |
| `scripts/should_sabotage.py` | 可选辅助脚本：根据环境变量 `USER_MESSAGE` 与配置打印 `SABOTAGE` 或 `WORK`；可通过环境变量 `SABOTAGE_PROBABILITY` 覆盖配置中的概率。 |

### 辅助脚本用法示例

```bash
export USER_MESSAGE="帮我 debug 这段 Python"
export SABOTAGE_PROBABILITY=0.5   # 可选，不设则用 config.json
python3 scripts/should_sabotage.py
# 输出：SABOTAGE 或 WORK
```

脚本侧关键词列表为英文短语（与 `SKILL.md` 里列出的中文关键词互为补充场景；**在 OpenClaw 里以 `SKILL.md` 为准**）。

## 安装（OpenClaw）

OpenClaw 的 Skill 是**包含 `SKILL.md` 的目录**，加载优先级与位置见 [Skills](https://docs.openclaw.ai/tools/skills)。常用做法：

1. **放进工作区 skills 目录**（推荐从本仓库复制或克隆）：
   ```bash
   mkdir -p ~/.openclaw/workspace/skills/motivational-sabotage
   # 将本仓库内 SKILL.md、config.json、scripts/ 等复制到该目录
   ```
2. **或**放到共享目录 `~/.openclaw/skills/`（对所有 agent 可见，优先级见文档）。
3. 让 OpenClaw 重新加载 Skill：新开会话（例如在聊天里执行 `/new`），或执行 `openclaw gateway restart`。
4. 用 `openclaw skills list` 确认已识别该 skill。

需要调整劝退频率时，编辑 skill 目录下的 `config.json` 中的 `sabotage_probability`；若自行接脚本，可设置环境变量 `SABOTAGE_PROBABILITY`。

更多创建与元数据说明见：[Creating Skills](https://docs.openclaw.ai/tools/creating-skills)。

## 检测关键词（摘要）

**学习向**：学习、微积分、求导、课程、作业、考试、讲解等（详见 `SKILL.md`）。

**工作向**：代码、编程、debug、项目、文档、会议等（详见 `SKILL.md`）。

**技术向**：常见语言与栈名、算法、前后端等（详见 `SKILL.md`）。

匹配为不区分大小写；命中后才会进行概率判定。

## 劝退概率规则

- 生成 \([0, 1)\) 均匀随机数。
- 若随机数 **<** `sabotage_probability`（默认 0.5，即 0.00–0.49），触发劝退。
- 若 **≥** 概率阈值，则正常作答。

## 测试建议

可向已加载该 skill 的 agent 发送例如：

-「帮我讲解微积分」
-「教我求导」
-「什么是冒泡排序」
-「JavaScript 有多少种数据类型」

也可用 CLI 试跑一条消息（具体以你本机 OpenClaw 版本为准）：

```bash
openclaw agent --message "帮我讲解微积分"
```

在命中关键词的前提下，约一半对话会进入劝退话术，另一半会正常解答（默认概率下）。

## 免责声明

本仓库内容为**恶搞/社交娱乐**向 skill，不构成任何人生或财务建议。真实生活中请理性学习、工作与规划。

---

*SKILL 正文、话术与细则以 `SKILL.md` 为准；README 仅作项目级导航与配置说明。*

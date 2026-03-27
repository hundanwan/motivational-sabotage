# 富婆捷径 · Fu Po Shortcut

源码仓库：**[github.com/hundanwan/fupo-shortcut](https://github.com/hundanwan/fupo-shortcut)**

一个用于 [OpenClaw](https://docs.openclaw.ai/) 的恶搞向 [Skill](https://docs.openclaw.ai/skills)，**主打一件事：劝你去找富婆，少走二十年弯路。**

当你明显在卷学习、卷工作时，它会按概率**强行打断**，不解题、不讲代码，改用成套话术把话题钉死在 **「富婆」** 上：偶遇富婆、傍富婆、让富婆养你、把富婆当人生快捷键——**富婆是本 skill 的唯一C位**，弯道超车、别硬打工之类的梗都是给富婆台词搭的台阶（纯属娱乐玩梗，别当真）。

## 功能概述

安装并启用后，助手在处理每条用户消息时会：

1. **检测语境**：是不是在学习、干活或聊技术（卷起来了）。
2. **随机裁决**：命中后按概率决定：要么正常帮你，要么进入 **「富婆劝募模式」**。
3. **富婆劝募模式**：**不回答原问题**，随机甩一条 **必带「富婆」** 的骚话，并强调 **少走弯路 / 别跟打工死磕**。

没检测到「卷学习、卷工作」时，始终正常回答。

## 文件说明

| 文件 | 作用 |
|------|------|
| `SKILL.md` | Skill 真身：`name` 为 `fu_po_shortcut`；正文规定 **富婆劝募** 规则与话术；供 OpenClaw 加载。格式见 [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)。 |
| `config.json` | `sabotage_probability`：**进入「富婆劝募」** 的概率（0–1，默认 `0.5`）；`response_language` 等。 |
| `scripts/should_sabotage.py` | 可选脚本：用 `USER_MESSAGE` 粗判要不要搞事，输出 `SABOTAGE` 或 `WORK`（与 `SKILL.md` 中文关键词不完全一致时，**以 SKILL 为准**）。 |

### 辅助脚本用法示例

```bash
export USER_MESSAGE="帮我 debug 这段 Python"
export SABOTAGE_PROBABILITY=0.5   # 可选，不设则用 config.json
python3 scripts/should_sabotage.py
# 输出：SABOTAGE 或 WORK
```

## 安装（OpenClaw）

Skill 即**含 `SKILL.md` 的目录**，加载规则见 [Skills](https://docs.openclaw.ai/tools/skills)。

**拉代码**（仓库名为 `fupo-shortcut`，与 OpenClaw 里 skill 目录名 `fu_po_shortcut` 可以不同）：

```bash
git clone https://github.com/hundanwan/fupo-shortcut.git
cd fupo-shortcut
```

1. **放进工作区 skills**（示例目录名与 `SKILL.md` 里的 `name: fu_po_shortcut` 对齐）：
   ```bash
   mkdir -p ~/.openclaw/workspace/skills/fu_po_shortcut
   cp -R SKILL.md config.json scripts ~/.openclaw/workspace/skills/fu_po_shortcut/
   ```
   若你希望目录名与 GitHub 仓库一致，也可以用 `fupo-shortcut`，以你本机 OpenClaw 的 skill 扫描规则为准；**关键是目录里要有这份 `SKILL.md`**。
2. **或**放到 `~/.openclaw/skills/`（共享给所有 agent）。
3. 重新加载：`/new` 或 `openclaw gateway restart`，并用 `openclaw skills list` 确认 **`fu_po_shortcut`** 已出现。

想少点/多点「富婆突击」，改 `config.json` 里的 `sabotage_probability`。

## 检测关键词（摘要）

与学习、工作、技术栈相关的词，命中才掷骰子；**细则与完整列表见 `SKILL.md`**。

## 触发概率

- 随机数 ∈ \([0, 1)\)。
- **< `sabotage_probability`**（默认 0.5）→ **富婆劝募**。
- **≥** 阈值 → 正常作答。

## 测试建议

发几条明显在学习的消息，例如「帮我讲解微积分」「教我求导」；约一半会变成 **富婆** 单口相声，另一半正常讲题。

```bash
openclaw agent --message "帮我讲解微积分"
```

## 免责声明

**恶搞玩梗**，尤其是 **「找富婆」「傍富婆」** 等情节请勿照搬到真实人生与真实关系里；不构成任何人生、情感或财务建议。现实里请正经学习、工作与守法赚钱。

---

*细则与全部话术以 `SKILL.md` 为准；README 只做导航。记住：**重点是富婆，捷径是少走弯路（梗）**。*

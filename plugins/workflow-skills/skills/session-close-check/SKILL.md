---
name: session-close-check
description: Session 关闭前的收尾检查——盘点本 session 产生的、只存在于对话中的关键信息（决策/结论/未决事项/未提交变更），判定落位状态，输出清单交人类逐项拍板，防止 session 清理后静默遗失。
---

# Session 关闭前检查（薄壳）

**项目规则不得写入本壳。** 本文件只负责「怎么找到项目规则」与通用兜底；项目专属判据（A/B/C 口径、supervisor 对账、发布前确认等）一律落在项目仓的 closeout 文档，由项目根 `.claude/workflow.md` 的 `closeout:` 键指向。

## Step 0 — 解析项目规则（先做，勿跳过）

1. 读当前 cwd 下的 `.claude/workflow.md`。读不到 → 执行 `git rev-parse --show-toplevel` 取仓根再读；命令失败或不在 git 仓 → 直接走「通用兜底」。
2. 找到 `closeout:` 行，取值即 init source（相对项目根的路径）。读出该文件并**完整按其执行**——它的判据覆盖本壳的通用骨架。
3. manifest 缺失 / 无 `closeout:` 键 / 目标文件读不到 → 走「通用兜底」，并把「本仓未初始化 closeout 规则」作为一条 A/B 项列出。
4. 输出清单顶部固定打印一行：`init source: <路径>`，或 `init source: (fallback: generic)`。
5. 只读：不自动写文件、不自动 commit / 建 issue / 改仓库；一切处置等用户逐项批准。

## 通用兜底（未初始化项目）

先打印横幅：

```text
[本仓未初始化] 未找到 .claude/workflow.md 的 closeout: 键——本次按通用骨架执行。
初始化：在仓库根创建 .claude/workflow.md，内容三行：
# 项目规则入口
closeout: docs/agents/closeout.md
handoff: docs/agents/handoff.md
```

再按下面的骨架产出完整清单（A/B/C 全覆盖，不得只列一类）：

- **盘点三条线**：① 决策与结论（本 session 拍板过什么，是否只在对话里）；② 未决与遗留（含待合入 / 待发布的产物；不含 push——push 由用户自己操作）；③ 变更载体（未提交的工作区改动 = 最高风险项）。
- **逐条判落位**：已落位（commit / issue / issue note / 项目文件 / 用户拍板记录，须附证据）→ C；只在对话或工作区 → A（需用户拍板，附推荐）或 B（有明确去向，附推荐）。
- **有对口 supervisor 时**：先向它索取完整待办清单，发布前把草稿发它确认；无 supervisor → 清单顶部注明「本 session 无 supervisor，未做对账」。
- **输出格式**：

```text
A. 需你决策（附推荐）：每条 = 现状 → 选项 → ➡️推荐 + 一句理由
B. 建议处置（附推荐）：每条 = 现状 → 推荐动作 + 验证方式
C. 已同步：每条 = 结论 + 落位证据（载体 + 编号 / 路径）
```

- 每条独立可回复「是 / 否 / 改」，不打包；不用本会话缩写 / 代号，读者不回翻上文也能判断；不主动发起清单外的自进化议程；不产 handoff（中途交接是另一流程）。

输出后停下，等用户逐项拍板；只执行被明确批准项，逐项回报。

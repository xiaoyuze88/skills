---
name: handoff-supervise
description: 给 /handoff 读的补充 prompt——按开工即 AFK 语义在交接文档里交代 supervisor 协作方式（疑问先问 supervisor；产品策略维度与需人类授权的动作才停等人类），并把本 session 转为 supervisor。读项目 .claude/workflow.md 的 handoff: 指向的项目规范并内联进交接文档；缺失时横幅 + 模板 + 通用兜底。用法：/handoff 读 /my-skills:handoff-supervise 并按其执行
disable-model-invocation: true
---

# supervisor 补充（薄壳，由 /handoff 读取后执行）

**项目规则不得写入本壳。** 本文件只负责「怎么找到项目规则」与通用兜底；项目专属的交接字段、路由与复盘要求落在项目仓的 handoff 文档，由项目根 `.claude/workflow.md` 的 `handoff:` 键指向。

## Step 0 — 解析项目规则（先做，勿跳过）

1. 读当前 cwd 下的 `.claude/workflow.md`。读不到 → 执行 `git rev-parse --show-toplevel` 取仓根再读；命令失败或不在 git 仓 → 通用兜底。
2. 找到 `handoff:` 行，取值即 init source（相对项目根的路径）。读出该文件，按其要求写交接文档。
3. 缺失 / 无 `handoff:` 键 / 目标文件读不到 → 走「通用兜底」，并在交接文档里注明「项目未初始化 handoff 规则」。
4. 交接文档顶部固定打印一行：`init source: <路径>` 或 `init source: (fallback: generic)`。

## 写交接文档

- **项目规则必须内联成文字写进交接文档**——接手方可能在别的项目 / worktree，manifest 路径是死引用，不得只写「见 docs/agents/handoff.md」。
- **通用三句**（项目规范未覆盖时补写）：① 有任何疑问先问本 session（契约解释 / 判据取舍 / 形态选择 / 计划外发现的定性）；**首条消息优先用消息信封的 `from-name` 寻址，信封没有 `from-name` 时再在正文补自己的会话名**；supervisor 裁不了的两类——产品策略维度、需人类授权的动作——停下记「待人类」。② 开工计划先发 supervisor 放行后动手；完工后找它 review，有问题就修，循环到通过；review 通过后再跑 e2e / 验收。③ supervisor 不写代码，只答疑和 review。
- **名字寻址**：交接单只写 supervisor 名字，不写死 ref；发送前 `ListAgents` 实时解析；同名歧义先 rename，仍歧义 fail-closed 问人类。
- **完工报告只报事实**：分支 / commit / worktree、`git diff --stat` 原文、跑过的命令 + 原始输出 / 日志路径、未解决 / BLOCKED / 待人类；禁止「应该没问题」「测试通过」这类没有输出的判断。
- **supervisor 独立复核**：至少独立跑 `git status --porcelain` + `git diff --stat` + 读声称改过的文件 diff；AC / 高风险才重跑门禁；结论写清覆盖范围（repo / 交接单 / 临时文件）。
- **投递兜底**（写进同一节）：回执「held for approval」→ 不等，按最小改动、维持现状的保守默认继续，问题原文记「待人类」；回执「查无此会话」→ 按名字重解析发一次，仍不通同保守默认；**开工计划放行与完工 review 两关例外**——未获答复停在该关卡，不许自行放行。
- **无临时目录可写**：把交接正文直接输出到对话，不写文件。
- **投递边界**：产出止于交接文档 + 向用户汇报；投递给谁、怎么投递由人类决定，未获当轮明确指示不主动向任何 session 发送消息。
- 写完 handoff 后本 session 转为 supervisor：答疑、review、循环修复；人类直接授权记录原话 / 时间 / 范围，review 时按记录核对。
- 多 session（主 / 辅助 supervisor）模式：接手方只与主通信，辅助结论由主带原话转达，主不替辅助定 AC。

## 通用兜底（未初始化项目）

先打印横幅：

```text
[本仓未初始化] 未找到 .claude/workflow.md 的 handoff: 键——按通用兜底执行。
初始化：在仓库根创建 .claude/workflow.md，内容三行：
# 项目规则入口
closeout: docs/agents/closeout.md
handoff: docs/agents/handoff.md
```

再按上面的通用三句 + 投递兜底 + 投递边界执行。交接文档必填节至少：任务坐标（分支 / commit / worktree）、待办与验收口径、supervisor 地址与协作方式、待人类事项。

## 不做

- 不写业务内容（交接什么由当前上下文决定；不重复其它 artifact，用路径或 URL 引用）。

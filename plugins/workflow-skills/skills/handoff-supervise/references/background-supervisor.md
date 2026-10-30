# 后台 supervisor（通用机制）

**只由 `handoff-supervise` 在 supervisor 已 `/background`（或准备后台运行）时读取。** 本文件是通用机制；项目专属交接字段、tracker、AFK 路由仍以项目 `handoff.md` 为准。

## 生命周期

1. 人类先写交接文档，再执行 `/background` 把 supervisor 退到后台；`/background` 是**人类动作**，agent 不自行执行。
2. 人类在同一窗口启动 implementer；supervisor 用 `ListAgents` 找到 `kind=bg` 的那行（名字保留，session id / ref 会变）。
3. 按名字 `SendMessage` 计划 → 放行 → 等完工报事实 → 独立 review → 人类 pin / logs / attach / stop / rm。
4. **旧 id 不要 resume**；`claude --resume <id> --bg` 保留原 id/ref 的说法（2.1.257+）未实测，不依赖。

## 名字与 ref

- **交接文档只写 supervisor 名字，不写死 ref**；ref 以发送时 `ListAgents` 实时解析为准。
- 后台会话按 `kind=bg` 过滤；同名歧义先 rename 成唯一名，仍歧义 fail-closed 问人类，不猜。
- 消息信封带 `from-name` 时优先用它寻址；解析不到再在正文补自己的会话名。

## 存活与 attach

- 长跑 supervisor 建议由人类 **pin（Ctrl+T）**；约 1h 空闲后 session 可能被停，`SendMessage` 能否唤醒已停 session 未实测，pin 是当前可靠的规避。
- `attach` / `Ctrl+Z`、`logs`、`stop`、`rm` 由人类在需要时使用；supervisor 只按名字收发消息。
- 回执「held for approval」：后台没有窗口可点，**held 等同不可达**；不要等。优先让收发双方使用**同一启动方式**；必须改 `crossSessionInbound: "accept"` 时，要求人类逐次批准——**agent 不自动改权限设置**。

## 完工报告事实契约

接手方完工报告只报事实：

- 分支 / commit / worktree 坐标；
- `git diff --stat <base>...<branch>` 原文；
- 跑过的命令 + 原始输出 / 日志路径；
- 未解决 / BLOCKED / 待人类。

**禁止**「应该没问题」「测试通过」「已自查」这类没有输出的判断。缺 `git diff --stat` 或命令输出时，supervisor 拒收并要求补事实。

## Supervisor 独立复核

- 至少独立跑 `git status --porcelain` + `git diff --stat <base>...<branch>` + 读声称改过的文件 diff；
- AC 承载 / 高风险改动才重跑门禁；其余不重复全量；
- review 结论写清覆盖范围（repo 工作区 / 交接单 / 临时文件），不写「双方均无改动」这类过宽表述；
- 不采信纯自述；报告与 diff 冲突 → fail-closed。

## Workspace-less / 小项目

- 项目没有 `.claude/workflow.md` / 不是 git 仓：按项目 `handoff.md` 缺失时的通用兜底；`session-close-check` 同理。
- 没有临时目录可写：交接正文直接输出到对话，不写文件。

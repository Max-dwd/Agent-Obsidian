# Delegate Protocol

`Delegates/` 是统一的人类 review 队列。凡是需要 agent 执行、交付、人类批准、后续继续做的事项，都写成一个独立 delegate page。weekly review、calendar 修改草稿、创建 project 后继续 delegate，也都走这个队列。

Source of truth 是本文件和 `Delegates/*.md`。Skill 可以帮助创建或更新页面，但不能替代本协议。

## 文件位置

每个 delegate task 必须有且只有一个独立页面：

```text
Delegates/{YYYY-MM-DD-HHMM-short-slug}.md
```

- `YYYY-MM-DD-HHMM` 使用创建时的本地时间。
- `short-slug` 由 agent 根据任务内容总结，使用短英文 slug。
- 如果同一分钟同名冲突，在 slug 后加 `-2`、`-3`。
- 不要发明额外 workflow bucket。产物放 `Artifacts/`，长期项目语境放 `Projects/`，然后从 delegate page 链接。

## Status Model

允许的 `status` 只有：

```yaml
need_review
done
archived
```

- `need_review`：agent 已交付，但需要人类 review、确认、批准、补资料、做决策，或原始目标尚未完成。
- `done`：人类已明确接受/批准，或 agent 已完成原始目标且没有阻塞、没有需要人类决策的事项、没有完成原始目标所需的 `Next` step。
- `archived`：人类认为不用再管，已从默认 review 队列清理。

Agent 结束交付时必须先判断是否真的需要人类 review。信息同步、抓取结果、状态报告、已完成的小修复等任务，如果已经完成原始目标，并且正文不需要 `Needs Review` 或 `Next`，应直接设为 `done`，不要进入默认 review 队列。只有需要人类确认、批准、决策、补资料，或目标未完成时，才设为 `need_review`。只要任务尚未 `archived`，且人类在 Properties 里勾选 `archive: true`，agent 就可以改成 `archived`。

`archive` 是人类批准归档的属性。Agent 不主动把 `archive` 从 `false` 改成 `true`。正文里的 `Needs Review` 和 `Next` checkbox 也是人类批准信号，agent 不主动勾选。

## Required Frontmatter

每个 delegate page 必须使用这个 frontmatter 形状。下面只示范字段完整性；`status` 不是固定默认值，必须按 Status Model 判断。

```yaml
---
id: "2026-05-17-1430-example-task"
type: delegate
task_kind: general
title: "Short task title"
status: need_review
archive: false
created_at: 2026-05-17T14:30
created_from: "chat:/Users/h/path/or/context"
updated_at: 2026-05-17T15:10
archived_at: null
parent_delegate: null
follow_ups: []
related_projects: []
related_delegates: []
source_links: []
artifacts: []
---
```

字段说明：

- `id`：与文件名一致，不含 `.md`。
- `type`：固定为 `delegate`。
- `task_kind`：用于区分任务类型。初始可用值：`general`、`calendar`、`weekly_review`、`project_setup`、`follow_up`。
- `status`：只能是 `need_review`、`done`、`archived`。
- `archive`：Obsidian Properties 里的 checkbox/boolean。默认 `false`。人类勾选为 `true` 后，agent 才能把未归档任务的 `status` 改成 `archived`。
- `created_from`：简单 string。推荐格式是 `chat:/absolute/working/dir` 或 `agent:/absolute/working/dir`。
- `parent_delegate`：follow-up task 指向原 delegate page；没有则为 `null`。
- `follow_ups`：原 delegate page 链接后续 delegate pages。
- `related_projects`、`related_delegates`、`source_links`、`artifacts`：用相对路径或 URL string，不要只靠正文里的模糊链接。

## Required Body

正文必须使用这个基本结构：

```markdown
# Task

What the agent was asked to do.

# Response

Your response to the delegation from human. Lead with the user-facing result, then mention methodologies, files changed, commands run, links, screenshots, and artifact links when relevant.

# Human Comment
```

## Response Detail Contract

`# Response` 不是运行日志区。写作顺序必须是：先交付用户在意的内容，再给证据；运行监控、命令、路径、失败重试、对比算法、缓存状态等明细默认折叠。

保持展开的内容：

- 结论：完成了什么、有没有完成原始目标、是否有新增/变化/风险。
- 用户真正要判断的事实：更新数量、监控对象变化、deadline、待办、决定、阻塞、需要采取的下一步。
- 人类会直接点开的链接：来源链接、artifact、截图、报告、关键文件。
- 短表格或短列表，只要它直接回答任务问题。

默认折叠的内容：

- 运行环境、权限、联网、沙盒、重试、rate limit、cache/reuse 状态。
- 命令、参数、完整路径、导出文件清单、对比基准文件清单。
- “如何确认”的细节：diff/signature 口径、验证命令、测试输出摘要、日志摘录。
- 支撑可信度但不需要每天展开看的内容。

Obsidian 默认收起 callout 使用这个格式，注意 `-` 表示默认收起：

```markdown
> [!note]- 运行与验证明细
> - 命令：`...`
> - 输出摘要：...
> - 文件：`/absolute/path`
```

可用标题示例：

- `> [!note]- 运行与验证明细`
- `> [!info]- 来源与对比口径`
- `> [!warning]- 阻塞诊断`

不要把真正的交付藏进 callout。比如周期性刷新任务里，“本次有多少新增/更新”“哪些变化值得处理”“哪些来源值得点开”必须展开；抓取命令、export 路径、比较算法、DNS/联网说明则应放进默认收起的 callout。

如果任务没有新增变化，也要在展开区清楚写出 `0 新增 / 0 内容级更新` 这类结论；不要让人必须展开运行明细才能知道结果。

`# Human Comment` 是人类可写区。人类可能直接在这个标题下面写自由文本，也可能写在下面的可选小节里。任何 follow-up / 继续执行前，agent 必须读取并遵守这里的人类 comment，把它当作最新的人类指令和 review 反馈。

`Needs Review` 和 `Next` 是可选小节，不是默认模板噪音。只有满足条件时才写。正文是否需要这两个小节，也决定 frontmatter 的 `status`：

- 只有在项目目标受阻、需求不清晰、缺少权限/资料、或 agent 无法合理选择如何达成目标时，才写 `Needs Review`。不要把普通建议、锦上添花、泛泛“请确认”塞进这里。
- 只有在原始目标尚未完成、并且存在明确后续步骤时，才写 `Next`。如果任务已经完成，不要为了显得完整而推荐下一步。
- 如果 `Needs Review` 或 `Next` 任一小节存在，通常应设为 `status: need_review`。
- 如果两个小节都不需要，且 agent 已完成原始目标，通常应设为 `status: done`。

```markdown
## Needs Review

- [ ] Specific human decision or unblocker needed.

## Next

- [ ] Concrete next step required to complete the original goal.
```

如果 `Needs Review` 或 `Next` 不需要，就直接省略该小节。不要写 `None.`，也不要制造空 checklist。

归档批准使用 frontmatter 里的 `archive` 属性，不使用正文 checkbox。人类把 `archive` 勾选为 `true` 表示“不用继续 review / 不用继续保留在处理队列”。Agent 看到任何非 `archived` delegate 满足 `archive: true` 后，才可以把 frontmatter 改为 `status: archived` 并填写 `archived_at`。

## Follow-Up Rule

用户勾选 `# Next` 里的事项，或在 `# Human Comment` 里写明需要继续后，继续任务时默认新建一个 follow-up delegate page，而不是无限追加到原页面。

Follow-up 前必须读取原页面的 frontmatter、`# Human Comment`、`## Needs Review`、`## Next`、`related_*` 和 `artifacts`。其中 `# Human Comment` 优先级最高，因为它是人类最新反馈。

原页面更新：

```yaml
follow_ups:
  - "Delegates/2026-05-17-1530-continue-example-task.md"
```

新页面设置：

```yaml
task_kind: follow_up
parent_delegate: "Delegates/2026-05-17-1430-example-task.md"
```

这样可以保留原始 review 记录，也方便 Dataview 查询当前仍需要处理的页面。

## Dataview Hints

默认 review 队列：

```dataview
TABLE task_kind, title, updated_at
FROM "Delegates"
WHERE type = "delegate" AND status = "need_review" AND archive != true
SORT updated_at DESC
```

Done 但未勾归档：

```dataview
TABLE archive, task_kind, title, updated_at
FROM "Delegates"
WHERE type = "delegate" AND status = "done" AND archive != true
SORT updated_at DESC
```

已批准归档，待 agent 清理：

```dataview
TABLE archive, task_kind, title, updated_at
FROM "Delegates"
WHERE type = "delegate" AND status != "archived" AND archive = true
SORT updated_at DESC
```

已归档不进入默认队列：

```dataview
TABLE task_kind, title, archived_at
FROM "Delegates"
WHERE type = "delegate" AND status = "archived"
SORT archived_at DESC
```

## Skill Contract

Skill 的职责是：

- 快速定位本 vault。
- 读取本协议。
- 创建或更新 `Delegates/*.md`。
- 在需要时读取相关 `Projects/`、`Artifacts/`、`related_*` 链接。
- 调用其他 vault-local skill 前，仍以本协议为写入契约。

当前工作目录里的 repo / project 规则优先管代码行为；本 vault 的规则只管记录、委托、review、follow-up。

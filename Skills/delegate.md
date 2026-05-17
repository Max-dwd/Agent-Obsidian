# Delegate Skill Contract

这个文件描述未来 `delegate` skill 应该做什么。实际执行规则以 [../protocols/delegate.md](../protocols/delegate.md) 为准。

## Use When

- 用户要求把任务交给 agent。
- 用户说“继续做某事”，且这件事需要从旧 delegate / project / artifact 里找上下文。先找到delegate，然后从formatter里找到其余上下文。
- 用户勾选了某个 delegate page 的 `# Next` 项，需要继续执行。
- weekly review、calendar 修改、project setup 的结果需要进入人类 review 队列。

## Procedure

1. 定位 Agent Obsidian vault。
2. 读取 `protocols/delegate.md`。
3. 如果是模糊继续任务，优先查 `Delegates/` 的 frontmatter：`status`、`task_kind`、`parent_delegate`、`follow_ups`、`related_projects`、`related_delegates`、`source_links`、`artifacts`。
4. 只在 frontmatter 不够时再全文搜索。
5. 创建或更新 delegate page。
6. 写 `# Response` 时先展开用户在意的交付内容；把运行监控、命令、完整路径、对比口径、重试/联网/缓存等明细放进默认收起的 Obsidian callout，例如 `> [!note]- 运行与验证明细`。
7. 交付前判断是否需要人类 review：如果需要人类确认、批准、决策、补资料，或原始目标尚未完成，设为 `status: need_review`。
8. 如果 agent 已完成原始目标，且没有 `Needs Review`、没有 `Next`、没有阻塞或待人类决策事项，设为 `status: done`。
9. 只有人类勾选 `archive: true` 后，才设置 `archived`。

## Non-Goals

- 不替代当前 repo 的 `AGENTS.md` 或工程规则。
- 不把大段原始日志写进 delegate page。
- 不把真正的交付结果藏进折叠 callout；callout 只放运行和验证明细。
- 不主动勾选人类 approval checkbox。

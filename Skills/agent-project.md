# Agent Project Skill Contract

这个文件描述未来 `agent-project` skill 应该做什么。Project skill 不能单独执行任务；任何 project create / continue 都必须先走 [../protocols/delegate.md](../protocols/delegate.md)，再按 [../protocols/project.md](../protocols/project.md) 做附加项目记录。

## Use When

- 用户明确要求创建一个 Agent Obsidian project。
- 用户要求继续某个 project，或从 project 上下文恢复任务。
- 用户要求把工作状态、成果、风险、下一步写进 project。
- 用户要求整理某个 project 的下一步、阻塞、成果链接或长期语境。
- 用户提到旧入口 `ENTRY！！！.md` / `status-protocol.md` 的项目账本理念，并想迁移到当前 vault。

## Procedure

1. 定位 Agent Obsidian vault。
2. 读取 `README.md` 和 `protocols/delegate.md`。
3. 先创建或更新当前任务的 delegate page；project create 通常使用 `task_kind: project_setup`，project continue 通常使用 `task_kind: follow_up` 或 `general`。
4. 读取 `protocols/project.md`。
5. 如果是继续已有项目，查 `Projects/`，优先按项目名、frontmatter、最近状态和相关链接定位唯一项目页。
6. 读取项目页的 frontmatter、`# Context`、最近状态条目，以及当前任务相关的 `artifacts` / `related_delegates` / `source_links`。
7. 读取当前 delegate 的 `# Human Comment`、`## Needs Review`、`## Next`；这些人类反馈优先于项目页旧状态。
8. 只有用户明确说“创建项目”或“create project”时，才允许创建新的 `Projects/*.md`；否则找不到唯一项目页就在 delegate 中说明需要指定已有项目或明确要求创建。
9. 更新已有项目页，或在明确创建请求下创建新项目页；project page 只写摘要、证据、成果链接和当前 delegate 链接。
10. 在 delegate frontmatter 的 `related_projects` 写入 project 路径，在 project frontmatter 的 `related_delegates` 写入 delegate 路径。
11. 写完后快速回读目标 delegate 和 project page，确认互链和更新落在正确文件。

## Non-Goals

- 不替代当前 repo 的 `AGENTS.md` 或工程规则。
- 不把 `Skills/agent-project.md` 当作 source of truth；具体格式和状态规则永远读 `protocols/project.md`。
- 不绕过 delegate；project page 不能作为任务入口或任务生命周期记录。
- 不把原始日志、聊天记录或大段命令输出直接塞进项目页。
- 不把需要 review / follow-up 的工作留在项目页里悬空；这类事项必须进入 `Delegates/`。

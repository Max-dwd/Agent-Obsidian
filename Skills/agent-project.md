# Agent Project Skill Contract

这个文件描述未来 `agent-project` skill 应该做什么。实际执行规则以 [../protocols/project.md](../protocols/project.md) 为准；需要 review / follow-up 时再读取 [../protocols/delegate.md](../protocols/delegate.md)。

## Use When

- 用户明确要求创建一个 Agent Obsidian project。
- 用户要求继续某个 project，或从 project 上下文恢复任务。
- 用户要求把工作状态、成果、风险、下一步写进 project。
- 用户要求整理某个 project 的下一步、阻塞、成果链接或长期语境。
- 用户提到旧入口 `ENTRY！！！.md` / `status-protocol.md` 的项目账本理念，并想迁移到当前 vault。

## Procedure

1. 定位 Agent Obsidian vault。
2. 读取 `README.md`，确认当前任务应该走 project protocol。
3. 读取 `protocols/project.md`。
4. 如果是继续已有项目，先查 `Projects/`，优先按项目名、frontmatter、最近状态和相关链接定位唯一项目页。
5. 读取项目页的 frontmatter、`# Context`、最近状态条目，以及当前任务相关的 `artifacts` / `related_delegates` / `source_links`。
6. 如果任务来自 delegate 的 `# Human Comment`、`## Needs Review` 或 `## Next`，读取对应 delegate，并让这些人类反馈优先。
7. 只有用户明确说“创建项目”或“create project”时，才允许创建新的 `Projects/*.md`；否则找不到唯一项目页就停止并说明需要指定已有项目或明确要求创建。
8. 更新已有项目页，或在明确创建请求下创建新项目页；长内容写入 `Artifacts/` 后再链接。
9. 如果需要人类 review、批准、决策、补资料，或需要后续 agent 继续执行，读取 `protocols/delegate.md` 并创建或链接 delegate。
10. 写完后快速回读目标项目页，确认更新落到正确项目。

## Non-Goals

- 不替代当前 repo 的 `AGENTS.md` 或工程规则。
- 不把 `Skills/agent-project.md` 当作 source of truth；具体格式和状态规则永远读 `protocols/project.md`。
- 不把原始日志、聊天记录或大段命令输出直接塞进项目页。
- 不把需要 review / follow-up 的工作留在项目页里悬空；这类事项必须进入 `Delegates/`。
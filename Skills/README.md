# Skills

这里记录 vault-local skill 的设计入口。真正的 Codex Skill 可以之后再安装到 Codex 的 skill 目录；安装后的 skill 也应该读取本 vault 的 protocol，而不是复制一份独立规则。

当前约定：

- [delegate.md](delegate.md)：所有任务的强制入口；创建、更新、继续 delegate page。
- [agent-project.md](agent-project.md)：只有用户明确要求创建 / 继续 project 时使用；在 delegate 之外额外更新 project page。
- [calendar-link.md](calendar-link.md)：在 Google Calendar connector 可写的日历里创建/修改事件，并把事件链接回 delegate / project / artifact。
- 未来可以增加 `weekly-review.md`，但它们需要 review / follow-up 时仍写入 `Delegates/`。

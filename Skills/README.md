# Skills

这里记录 vault-local skill 的设计入口。真正的 Codex Skill 可以之后再安装到 Codex 的 skill 目录；安装后的 skill 也应该读取本 vault 的 protocol，而不是复制一份独立规则。

当前约定：

- [delegate.md](delegate.md)：创建、更新、继续 delegate page 的入口。
- [agent-project.md](agent-project.md)：创建、继续、更新 project page 的入口。
- 未来可以增加 `calendar.md`、`weekly-review.md`，但它们需要 review / follow-up 时仍写入 `Delegates/`。

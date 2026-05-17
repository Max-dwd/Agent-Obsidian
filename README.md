# Agent Obsidian

这个 vault 是给人类和 agent 共用的工作交接层。外部 agent 进入这里时，先读本文件，再读具体 protocol。

## 入口

- 人类当前处理面板：看 [DASHBOARD.md](DASHBOARD.md)。不要再假设入口面板叫 `Today.md`。
- Project create / project continue / 长期项目语境维护：读 [protocols/project.md](protocols/project.md)，写入 [Projects/](Projects/)。
- Delegate / follow-up / weekly review / calendar draft / 需要人类 review 的 project setup：读 [protocols/delegate.md](protocols/delegate.md)。
- 需要产生给人看的长文、图片、网页、表格、报告时，放进 [Artifacts/](Artifacts/) 并从对应 project 或 delegate page 链接。
- Skill 只做快捷入口和执行封装；source of truth 永远在本 vault 的 protocol 文件里。

## 默认规则

- 默认用中文写给人类看的内容；路径、命令、API 字段、代码标识符、工具名、原始错误可以保留英文。
- 不要把原始日志直接塞进正文。先整理成能让人快速判断状态、风险、下一步的内容。
- Project page 只保存长期语境、状态摘要、证据和成果链接；长内容放进 `Artifacts/`，需要 review / follow-up 的事项放进 `Delegates/`。
- Delegate 正文先展开真正的交付和用户在意的结论；运行监控、命令、路径、对比口径等明细用 Obsidian 默认收起 callout，例如 `> [!note]- 运行与验证明细`。
- 不要凭空创建新的 workflow bucket。需要 review 或 follow-up 的工作统一进入 [Delegates/](Delegates/)。

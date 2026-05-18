# Project Protocol

`Projects/` 保存长期项目语境和状态账本。它用于在用户明确要求创建 / 继续 project 时，额外记录项目背景、状态摘要、delegate 链接和成果材料；它不替代执行目录、repo、delegate 任务队列或完整日志。

Source of truth 是本文件和 `Projects/*.md`。Skill 可以帮助创建或更新项目页，但不能替代本协议。

Project protocol 不能单独执行任务。任何创建 / 继续 project 的请求，都必须先按 `protocols/delegate.md` 创建或更新 delegate page，再把项目摘要和链接写入 `Projects/`。

## 文件位置

每个项目使用一个独立页面：

```text
Projects/{short-project-name}.md
```

- `short-project-name` 用能稳定识别项目的短名称；已有项目优先复用现有文件名，不要因为大小写、空格或轻微改名创建重复项目。
- 详细报告、图片、网页、表格、长计划等放进 `Artifacts/`，再从项目页链接。
- 需要人类 review、批准、补资料、做决策，或需要后续 agent 继续执行的事项，写入 `Delegates/`，再从项目页链接。
- 不要发明额外 workflow bucket。

## Required Frontmatter

新项目页必须使用这个最小 frontmatter：

```yaml
---
type: project
title: "Project title"
status: active
created_at: 2026-05-17T14:30
updated_at: 2026-05-17T14:30
related_delegates: []
artifacts: []
source_links: []
---
```

字段说明：

- `type`：固定为 `project`。
- `title`：人类可读项目名。
- `status`：默认 `active`。如果项目明确暂停、完成或归档，可用 `paused`、`done`、`archived`。
- `created_at` / `updated_at`：使用本地时间。
- `related_delegates`：相关 delegate page 的相对路径。
- `artifacts`：相关成果材料的相对路径。
- `source_links`：外部 URL、repo 路径、课程页面、邮件、issue、PR、笔记等来源。

Frontmatter 可以随项目状态更新，但正文状态记录应保持 append-only。不要重写无关历史条目。

## Required Body

新项目页正文使用这个基本结构：

```markdown
# Context

这个项目是什么，真实工作目录在哪里，关键入口和约束是什么。

# Status Log

## 2026-05-17 — Short status title — active

**上下文：**
说明工作发生在哪里。包含执行目录、repo 路径、课程/系统入口或其他定位信息。

**结果：**
先写人类最需要知道的结论：完成了什么、还差什么、风险是什么。

**证据：**
写清支持结论的内容，例如命令、测试、改动文件、来源链接、精确阻塞文本。

**交付结果：**（如有）
链接到 `Artifacts/` 或执行目录里的成果材料。

**相关任务：**
链接当前 delegate page。Project page 不承载任务正文，只指向 delegate。

**下一步行动：**（如有）
下一项具体步骤。需要 agent 后续执行时，写到 delegate 的 `Next` 或 follow-up delegate 里；project page 只保留摘要和链接。
```

如果已有项目页结构不同，不要为了套模板重写整页。优先保留原文，在最合适的位置追加新的状态条目；必要时只补缺失 frontmatter 或明显缺失的顶层标题。

## 创建项目

只有用户明确要求“创建项目”或“create project”时，agent 才能创建新的 `Projects/*.md`。创建 project 仍必须先走 delegate：先创建或更新 `Delegates/*.md`，再在 project page 里记录摘要和 delegate 链接。

如果用户只是说“继续 project”、“记录到 project”、“整理项目”或提到某个项目名，agent 必须先查找已有项目；找不到时在 delegate 中说明没有匹配项目，不要自动新建。

创建项目时：

1. 先按 `protocols/delegate.md` 创建或更新 delegate page；创建项目通常使用 `task_kind: project_setup`。
2. 再查 `Projects/` 是否已有同一项目或近似项目。
3. 如果已有，继续更新旧项目页，不创建重复页。
4. 如果没有，创建 `Projects/{short-project-name}.md`。
5. 写清项目目标、真实工作目录、关键来源、当前状态和下一步摘要。
6. 在 project frontmatter 的 `related_delegates` 写入当前 delegate 路径；在 delegate frontmatter 的 `related_projects` 写入当前 project 路径。

## 继续项目

继续 project 前必须先有当前 delegate。读取顺序：

1. 当前 delegate page 的 frontmatter、`# Task`、`# Human Comment`、`## Needs Review`、`## Next`、`related_*` 和 `artifacts`。
2. 项目页 frontmatter。
3. `# Context` 和最近的 `# Status Log` 条目。
4. project frontmatter 中的 `artifacts`、`related_delegates`、`source_links`，只读取和当前任务相关的内容。
5. 如果任务来自 delegate 的 `# Human Comment`、`## Needs Review` 或 `## Next`，这些人类反馈优先于项目页旧状态。

如果无法找到唯一项目页，且用户没有明确说“创建项目”或“create project”，在 delegate 中说明需要指定已有项目或明确要求创建项目，不要自动新建 project。

继续后的写入规则：

- 项目页只追加简洁状态、证据、成果链接和当前 delegate 链接，不塞原始日志，不复制 delegate 正文。
- 长内容先写成 `Artifacts/` 里的独立交付物，再在项目页链接。
- 任何 review / approval / decision / unblocker / follow-up execution 都写在 delegate 里；project page 只保留摘要和链接。

## Writing Rules

- 默认用中文写给人类看的内容；路径、命令、API 字段、代码标识符、工具名、原始错误可以保留英文。
- 不记录 secret、API key、private token、credential 或未脱敏私人内容。
- 不要把这个 vault 当作执行事实来源。执行事实仍来自真实 repo、命令输出、课程页面、issue、邮件、API 等原始来源。
- 不要把一堆终端日志、原始输出、聊天记录或未经整理的长文本直接写进项目页。
- 如果信息复杂，优先用 `Artifacts/` 做表格、报告、流程图、时间线、截图标注或其他结构化材料。
- 写完重要项目更新后，快速回读目标项目页，确认条目落在正确文件。

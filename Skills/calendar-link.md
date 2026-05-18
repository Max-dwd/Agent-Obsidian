# Calendar Link Skill Contract

这个文件描述 vault-local `calendar-link` skill 应该做什么。实际执行规则以 [../protocols/calendar-link.md](../protocols/calendar-link.md) 为准；需要 review / follow-up 时再读取 [../protocols/delegate.md](../protocols/delegate.md)。

## Use When

- 用户要求把日期、时间、会议、航班、预约或 deadline 写进 Google Calendar。
- agent 从邮件、delegate、project 或 artifact 里提取到明确日期/时间，需要创建或修改日历事件。
- Google Calendar 事件需要链接回 Agent Obsidian 的 delegate / project / artifact 页面。
- 用户说“添加到日历”“写进日历”“提醒我”“calendar link”或类似意图。

## Procedure

1. 定位 Agent Obsidian vault。
2. 读取 `README.md`，确认当前任务应该走 calendar-link。
3. 读取 `protocols/calendar-link.md`。
4. 读取相关 delegate / project / artifact 页面，拿到 vault 相对路径。
5. 确认 Google Calendar connector 当前有可见、可写的 calendar；如果只暴露 primary / default，就使用它。
6. 构造 Apple Calendar-safe 纯文本 event description/info；不要用 HTML / `<p></p>` / Markdown link，必须包含裸 `obsidian://open?vault=Agent%20Obsidian&file=...` 和路径 fallback。
7. 按协议判断 all-day、真实 duration、start-only 5 分钟默认，以及 reminder 策略。
8. 修改事件时，先从来源页面的 `## Calendar Links` 找 `event_id`。
9. 创建/修改成功后，把 event id/link 写回来源 delegate 或 project 页的 `## Calendar Links`。
10. 如果无法安全写入日历，按 `protocols/delegate.md` 创建或更新 `task_kind: calendar` 的 `need_review` delegate。

## Non-Goals

- 不替代 delegate / project / artifact 的创建流程。
- 不要求固定命名的 Google Calendar；只写入 connector 当前可见、可写的 calendar。
- 不在没有 Obsidian link 的情况下创建或修改事件。
- 不把 Calendar 当作独立任务系统；Calendar 只保存时间块和回链。
- 不安装或假设全局 Codex skill；本文件只是 vault-local skill contract。

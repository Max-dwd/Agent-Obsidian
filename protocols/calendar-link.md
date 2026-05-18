# Calendar Link Protocol

`calendar-link` 负责把已经明确的时间信息写入 Google Calendar，并把对应的 Agent Obsidian 页面链接放进事件说明。它是外部日历连接层，不替代 `Delegates/`、`Projects/` 或 `Artifacts/`。

Source of truth 是本文件。Skill 可以帮助调用 Google Calendar 和更新页面，但不能替代本协议。

## Scope

Use this protocol when:

- 用户要求把某个日期/时间写进日历。
- agent 从邮件、网页、项目资料或 delegate 执行结果里发现明确日期/时间，需要创建或修改日历事件。
- 已有 delegate / project / artifact 页面需要和 Google Calendar 事件互相链接。

Do not use this protocol when:

- 只有模糊待办，没有日期或时间；这类内容先走 `protocols/delegate.md` 或 `protocols/project.md`。
- 用户只是要整理项目语境或交付长文；这类内容仍写入 `Projects/` / `Artifacts/`。
- Google Calendar 事件没有任何可链接的 Agent Obsidian 页面；先创建或定位 delegate / project / artifact，再写日历。

## Calendar Target

Google Calendar 写入以 connector 当前可见、可写的 calendar 为准。不要要求某个固定命名的 calendar 必须存在。

- 如果 connector 只暴露 primary / default calendar，就使用这个可写 calendar。
- 如果 connector 暴露多个可写 calendar，且用户明确指定目标 calendar，使用用户指定的可见 calendar。
- 如果 connector 暴露多个可写 calendar，但用户没有指定目标，使用 connector 的 primary / default calendar。
- 如果没有任何可见、可写的 calendar，停止写入并转成 delegate review。
- 创建/修改成功后，writeback 里记录实际使用的 calendar name 或 calendar id。

## Apple Calendar-Safe Description

每个 Google Calendar event 的 description/info 必须使用 Apple Calendar 可读的纯文本格式。不要写 HTML，不要写 `<p></p>`、`<br>`、Markdown link 或富文本片段。

description/info 必须包含至少一个 Agent Obsidian 页面链接。链接格式以裸 `obsidian://` URL 为主，并附 vault 相对路径 fallback：

```text
Obsidian:
- Delegate: obsidian://open?vault=Agent%20Obsidian&file=Delegates%2F2026-05-17-1430-example.md
  Path: Delegates/2026-05-17-1430-example.md
```

Rules:

- `vault` 固定为 `Agent%20Obsidian`。
- `file` 使用 vault 相对路径，并做 URL percent-encoding，例如 `/` 写成 `%2F`，空格写成 `%20`。
- 允许同时链接多个页面，例如 delegate page、project page、artifact page。
- 如果 description/info 已有正文摘要，把 Obsidian link 放在正文后面独立区块；不要把链接埋进长段落。
- 用真实换行分隔段落和链接；不要用 HTML tag 或 Markdown 链接语法表达格式。
- 缺少 Obsidian link 时，不要创建或修改事件。

## Time Rules

- 只有日期、没有具体时间时，创建 all-day event。
- 有 start time 且有明确 end time / duration 时，使用来源里的真实结束时间或 duration。
- 有 start time 但没有 end time / duration 时，默认创建 5 分钟 timed event。
- 如果来源是航班、会议、预约、office hour 等通常有结束时间的事件，先尽量从来源解析真实 end time；解析不到时才使用 5 分钟默认。
- 时间解释使用当前任务上下文的时区；没有更具体来源时，用本地时区 `America/Los_Angeles`。

## Reminder Rules

Reminder 取决于任务来源：

- 自动化任务默认不设置 event-level reminder。包括 cron / monitor / 自动邮件读取 / 后台 agent 批处理。
- 非自动化任务默认设置 timed event 提前 10 分钟提醒。包括用户在当前 chat 里主动要求添加日历、总结航班并写日历、或明确说“提醒我”。
- All-day event 默认不设置 event-level reminder，除非用户明确要求。
- 用户明确指定提醒时间、不要提醒、或沿用日历默认提醒时，按用户指令覆盖默认。
- 不要为了提醒而创建额外事件或额外 delegate。

## Create / Modify Procedure

1. 读取 `README.md`，确认任务属于 calendar-link。
2. 如事件来自 delegate / project / artifact，读取相关页面，确认它们的 vault 相对路径。
3. 构造事件 title、start/end 或 all-day date、Apple Calendar-safe 纯文本 description/info、Obsidian links、reminder policy。
4. 调用 Google Calendar 前，确认至少有一个 connector 可见、可写的 calendar；按 Calendar Target 规则选择目标。
5. 修改已有事件时，优先从来源页面的 `## Calendar Links` 找 `event_id`；不要优先靠标题和时间模糊匹配。
6. 创建/修改成功后，把 Google event id/link 写回来源 delegate 或 project 页面。
7. 如果无法安全写入日历，按 `protocols/delegate.md` 写一个 `task_kind: calendar` 的 `need_review` delegate，说明阻塞原因和建议动作。

## Page Writeback

如果来源 delegate 或 project 页面存在，创建/修改成功后，在正文中维护一个 `## Calendar Links` 小节。不要重写无关历史。

推荐格式：

```markdown
## Calendar Links

- calendar: "primary"
  event_id: "google-event-id"
  event_link: "https://calendar.google.com/calendar/event?eid=..."
  action: created
  title: "Event title"
  time: "2026-05-17T14:30-07:00/2026-05-17T14:35-07:00"
  linked_pages:
    - "Delegates/2026-05-17-1430-example.md"
    - "Artifacts/flight-summary.md"
  updated_at: 2026-05-17T14:40
```

Rules:

- `calendar` 记录实际写入的 calendar name 或 calendar id。
- `event_id` 是后续修改的首选定位依据。
- `event_link` 是给人点击检查的 Google Calendar 链接；如果工具只返回 id，先写 id，说明 link unavailable。
- `linked_pages` 使用 vault 相对路径。
- 同一个页面有多个日历事件时，追加多个条目。
- 修改已有事件时，更新对应条目的 `action` 为 `updated`，并刷新 `title`、`time`、`linked_pages`、`updated_at`。

## Failure Handling

以下情况必须停止 Google Calendar 写入，并转成 delegate review：

- 没有任何可见、可写的 Google Calendar。
- 缺少可写 Google Calendar 权限。
- 事件 description/info 里无法加入 Obsidian link。
- 没有任何可链接的 delegate / project / artifact 页面。
- 时间信息不明确到无法判断日期或 start time。
- 修改事件时找不到 `event_id`，且标题/时间匹配有多个候选事件。

delegate review 应使用 `protocols/delegate.md`，设置 `task_kind: calendar`，在 `# Response` 里展开说明阻塞原因、已解析出的时间信息、相关 Obsidian 页面，以及人类需要补充或批准什么。

## Examples

自动邮件读取：

- 邮件 agent 创建 `Delegates/...flight-check.md`。
- 如果邮件里有具体航班时间，calendar-link 在 connector 可写 calendar 里创建航班事件。
- Event description 链接 delegate page；自动化任务默认不设置 reminder。
- 成功后把 event id/link 写回 delegate page 的 `## Calendar Links`。

用户手动航班总结：

- agent 创建或更新 delegate/project，并把航班汇总写入 `Artifacts/...md`。
- calendar-link 为每段航班创建事件。
- Event description 同时链接 delegate/project 和 artifact。
- 因为这是非自动化用户请求，timed event 默认提前 10 分钟提醒，除非用户说不要提醒。

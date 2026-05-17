# DASHBOARD

这个页面是当前的人类处理面板。默认看四类：

- `need_review` + `archive: false`：agent 已交付，等人类 review。
- `done` + `archive: false`：人类已接受，但还没批准归档。
- `archive: true`：人类已勾选归档，等 agent 清理状态。
- `archived`：已经清理，只保留查询入口。

## 需要 Review

```dataviewjs
addArchiveTableStyles();

const pages = dv.pages('"Delegates"')
  .where(p => p.type === "delegate" && p.status === "need_review" && p.archive !== true)
  .sort(p => p.updated_at ?? p.file.mtime, "desc")
  .array();

renderArchiveTable(pages, ["Kind", "Title", "From"], p => [
  p.task_kind ?? "",
  p.title ?? "",
  formatFrom(p.created_from)
]);

function renderArchiveTable(pages, headers, rowValues) {
  const table = dv.container.createEl("table");
  table.addClass("agent-obsidian-review-table");
  const thead = table.createEl("thead");
  const headRow = thead.createEl("tr");
  ["", "Updated", "Delegate", ...headers].forEach(h => headRow.createEl("th", { text: h }));
  const tbody = table.createEl("tbody");

  for (const page of pages) {
    const row = tbody.createEl("tr");
    const archiveCell = row.createEl("td");
    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = page.archive === true;
    archiveCell.appendChild(checkbox);
    checkbox.addEventListener("change", async () => {
      const file = app.vault.getAbstractFileByPath(page.file.path);
      if (!file) return;
      await app.fileManager.processFrontMatter(file, fm => {
        fm.archive = checkbox.checked;
      });
    });

    row.createEl("td", { text: formatDate(page.updated_at ?? page.file.mtime) });
    renderDelegateLink(row.createEl("td"), page);

    for (const value of rowValues(page)) {
      row.createEl("td", { text: String(value ?? "") });
    }
  }
}

function renderDelegateLink(cell, page) {
  const link = cell.createEl("a", {
    text: shortDelegateName(page.file.name),
    cls: "internal-link"
  });
  link.setAttr("data-href", page.file.path);
  link.addEventListener("click", event => {
    event.preventDefault();
    app.workspace.openLinkText(page.file.path, "", false);
  });
}

function shortDelegateName(name) {
  return String(name ?? "").replace(/^\d{4}-\d{2}-\d{2}-\d{4}-/, "");
}

function formatDate(value) {
  if (!value) return "";
  const date = value?.toJSDate ? value.toJSDate() : new Date(String(value));
  if (Number.isNaN(date.getTime())) return String(value);
  const pad = n => String(n).padStart(2, "0");
  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())} ${pad(date.getHours())}:${pad(date.getMinutes())}`;
}

function formatFrom(value) {
  if (!value) return "";
  const raw = String(value);
  const match = raw.match(/^([^:]+):(.*)$/);
  const kind = match ? `${match[1]}:` : "";
  const body = (match ? match[2] : raw).replace(/^\/Users\/h/, "~");
  const parts = body.split("/").filter(Boolean);
  if (parts.length <= 3) return `${kind}${body}`;
  return `${kind}.../${parts.slice(-3).join("/")}`;
}

function addArchiveTableStyles() {
  if (document.getElementById("agent-obsidian-review-table-style")) return;
  const style = document.createElement("style");
  style.id = "agent-obsidian-review-table-style";
  style.textContent = `
    .agent-obsidian-review-table th:first-child,
    .agent-obsidian-review-table td:first-child {
      width: 2.25rem;
      min-width: 2.25rem;
      max-width: 2.25rem;
      text-align: center;
      padding-left: 0.25rem;
      padding-right: 0.25rem;
    }
  `;
  document.head.appendChild(style);
}
```

## Done 但未勾 Archive

这些是已经接受的任务，但还没有被人类清理掉。勾选表格里的 checkbox 表示你同意归档。

```dataviewjs
addArchiveTableStyles();

const pages = dv.pages('"Delegates"')
  .where(p => p.type === "delegate" && p.status === "done" && p.archive !== true)
  .sort(p => p.updated_at ?? p.file.mtime, "desc")
  .array();

renderArchiveTable(pages, ["Kind", "Title", "From"], p => [
  p.task_kind ?? "",
  p.title ?? "",
  formatFrom(p.created_from)
]);

function renderArchiveTable(pages, headers, rowValues) {
  const table = dv.container.createEl("table");
  table.addClass("agent-obsidian-review-table");
  const thead = table.createEl("thead");
  const headRow = thead.createEl("tr");
  ["", "Updated", "Delegate", ...headers].forEach(h => headRow.createEl("th", { text: h }));
  const tbody = table.createEl("tbody");

  for (const page of pages) {
    const row = tbody.createEl("tr");
    const archiveCell = row.createEl("td");
    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = page.archive === true;
    archiveCell.appendChild(checkbox);
    checkbox.addEventListener("change", async () => {
      const file = app.vault.getAbstractFileByPath(page.file.path);
      if (!file) return;
      await app.fileManager.processFrontMatter(file, fm => {
        fm.archive = checkbox.checked;
      });
    });

    row.createEl("td", { text: formatDate(page.updated_at ?? page.file.mtime) });
    renderDelegateLink(row.createEl("td"), page);

    for (const value of rowValues(page)) {
      row.createEl("td", { text: String(value ?? "") });
    }
  }
}

function renderDelegateLink(cell, page) {
  const link = cell.createEl("a", {
    text: shortDelegateName(page.file.name),
    cls: "internal-link"
  });
  link.setAttr("data-href", page.file.path);
  link.addEventListener("click", event => {
    event.preventDefault();
    app.workspace.openLinkText(page.file.path, "", false);
  });
}

function shortDelegateName(name) {
  return String(name ?? "").replace(/^\d{4}-\d{2}-\d{2}-\d{4}-/, "");
}

function formatDate(value) {
  if (!value) return "";
  const date = value?.toJSDate ? value.toJSDate() : new Date(String(value));
  if (Number.isNaN(date.getTime())) return String(value);
  const pad = n => String(n).padStart(2, "0");
  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())} ${pad(date.getHours())}:${pad(date.getMinutes())}`;
}

function formatFrom(value) {
  if (!value) return "";
  const raw = String(value);
  const match = raw.match(/^([^:]+):(.*)$/);
  const kind = match ? `${match[1]}:` : "";
  const body = (match ? match[2] : raw).replace(/^\/Users\/h/, "~");
  const parts = body.split("/").filter(Boolean);
  if (parts.length <= 3) return `${kind}${body}`;
  return `${kind}.../${parts.slice(-3).join("/")}`;
}

function addArchiveTableStyles() {
  if (document.getElementById("agent-obsidian-review-table-style")) return;
  const style = document.createElement("style");
  style.id = "agent-obsidian-review-table-style";
  style.textContent = `
    .agent-obsidian-review-table th:first-child,
    .agent-obsidian-review-table td:first-child {
      width: 2.25rem;
      min-width: 2.25rem;
      max-width: 2.25rem;
      text-align: center;
      padding-left: 0.25rem;
      padding-right: 0.25rem;
    }
  `;
  document.head.appendChild(style);
}
```

## 已勾 Archive，待清理

这些是你已经勾了 `archive` 的任务。Agent 看到后可以把对应 delegate page 改成 `status: archived`，并填写 `archived_at`。

```dataviewjs
addArchiveTableStyles();

const pages = dv.pages('"Delegates"')
  .where(p => p.type === "delegate" && p.status !== "archived" && p.archive === true)
  .sort(p => p.updated_at ?? p.file.mtime, "desc")
  .array();

renderArchiveTable(pages, ["Status", "Kind", "Title", "From"], p => [
  p.status ?? "",
  p.task_kind ?? "",
  p.title ?? "",
  formatFrom(p.created_from)
]);

function renderArchiveTable(pages, headers, rowValues) {
  const table = dv.container.createEl("table");
  table.addClass("agent-obsidian-review-table");
  const thead = table.createEl("thead");
  const headRow = thead.createEl("tr");
  ["", "Updated", "Delegate", ...headers].forEach(h => headRow.createEl("th", { text: h }));
  const tbody = table.createEl("tbody");

  for (const page of pages) {
    const row = tbody.createEl("tr");
    const archiveCell = row.createEl("td");
    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = page.archive === true;
    archiveCell.appendChild(checkbox);
    checkbox.addEventListener("change", async () => {
      const file = app.vault.getAbstractFileByPath(page.file.path);
      if (!file) return;
      await app.fileManager.processFrontMatter(file, fm => {
        fm.archive = checkbox.checked;
      });
    });

    row.createEl("td", { text: formatDate(page.updated_at ?? page.file.mtime) });
    renderDelegateLink(row.createEl("td"), page);

    for (const value of rowValues(page)) {
      row.createEl("td", { text: String(value ?? "") });
    }
  }
}

function renderDelegateLink(cell, page) {
  const link = cell.createEl("a", {
    text: shortDelegateName(page.file.name),
    cls: "internal-link"
  });
  link.setAttr("data-href", page.file.path);
  link.addEventListener("click", event => {
    event.preventDefault();
    app.workspace.openLinkText(page.file.path, "", false);
  });
}

function shortDelegateName(name) {
  return String(name ?? "").replace(/^\d{4}-\d{2}-\d{2}-\d{4}-/, "");
}

function formatDate(value) {
  if (!value) return "";
  const date = value?.toJSDate ? value.toJSDate() : new Date(String(value));
  if (Number.isNaN(date.getTime())) return String(value);
  const pad = n => String(n).padStart(2, "0");
  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())} ${pad(date.getHours())}:${pad(date.getMinutes())}`;
}

function formatFrom(value) {
  if (!value) return "";
  const raw = String(value);
  const match = raw.match(/^([^:]+):(.*)$/);
  const kind = match ? `${match[1]}:` : "";
  const body = (match ? match[2] : raw).replace(/^\/Users\/h/, "~");
  const parts = body.split("/").filter(Boolean);
  if (parts.length <= 3) return `${kind}${body}`;
  return `${kind}.../${parts.slice(-3).join("/")}`;
}

function addArchiveTableStyles() {
  if (document.getElementById("agent-obsidian-review-table-style")) return;
  const style = document.createElement("style");
  style.id = "agent-obsidian-review-table-style";
  style.textContent = `
    .agent-obsidian-review-table th:first-child,
    .agent-obsidian-review-table td:first-child {
      width: 2.25rem;
      min-width: 2.25rem;
      max-width: 2.25rem;
      text-align: center;
      padding-left: 0.25rem;
      padding-right: 0.25rem;
    }
  `;
  document.head.appendChild(style);
}
```

## 已 Archived

这些不进入默认处理队列，只作为查阅入口。

```dataview
TABLE WITHOUT ID
  file.link AS "Delegate",
  task_kind AS "Kind",
  title AS "Title",
  archived_at AS "Archived"
FROM "Delegates"
WHERE type = "delegate" AND status = "archived"
SORT archived_at DESC
```

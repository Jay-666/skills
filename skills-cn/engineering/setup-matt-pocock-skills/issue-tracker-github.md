# Issue 跟踪器：GitHub

此仓库的 issue 和 PRD 均以 GitHub issue 形式存在。所有操作均使用 `gh` CLI。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --comments`，使用 `jq` 筛选评论并同时获取标签。
- **列出 issue**：使用带适当 `--label` 和 `--state` 筛选条件的 `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **添加 / 移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：`gh issue close <number> --comment "..."`

从 `git remote -v` 推断仓库；在 clone 内运行时，`gh` 会自动完成此操作。

## 作为 triage 入口的 Pull Request

**PR 作为请求入口：no。** _（若本仓库将外部 PR 视为功能请求，则设为 `yes`；`/triage` 会读取此标志。）_

设为 `yes` 时，PR 使用 `gh pr` 的对应命令，经历与 issue 相同的标签和状态：

- **读取 PR**：使用 `gh pr view <number> --comments`；diff 使用 `gh pr diff <number>`。
- **列出待 triage 的外部 PR**：运行 `gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`，仅保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的项（排除 `OWNER`/`MEMBER`/`COLLABORATOR`）。
- **评论 / 添加标签 / 关闭**：使用 `gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 issue 与 PR 共用编号空间，因此单独的 `#42` 可能是任一类型：先用 `gh pr view 42` 解析，失败后回退到 `gh issue view 42`。

## 当 skill 指示“发布到 issue 跟踪器”

创建 GitHub issue。

## 当 skill 指示“获取相关 ticket”

运行 `gh issue view <number> --comments`。

## Wayfinding 操作

供 `/wayfinder` 使用。**地图**是一个 issue，以**子** issue 作为 ticket。

- **地图**：带有 `wayfinder:map` 标签的单个 issue，正文包含 Notes / Decisions-so-far / Fog。使用 `gh issue create --label wayfinder:map`。
- **子 ticket**：通过 GitHub sub-issue 关联到地图的 issue（在 sub-issues endpoint 上使用 `gh api`）。若未启用 sub-issues，则将子项加入地图正文的任务列表，并在子项正文顶部写入 `Part of #<map>`。标签为 `wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。认领后，将 ticket 分配给推进工作的开发者。
- **阻塞**：使用 GitHub 的**原生 issue 依赖**，这是权威且在 UI 中可见的表示。通过 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加边，其中 `<blocker-db-id>` 是阻塞项的数字**数据库 id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，*不是* `#number` 或 `node_id`）。GitHub 通过 `issue_dependencies_summary.blocked_by` 报告依赖（仅开放的阻塞项，即实时门槛）。依赖不可用时，在子项正文顶部回退为 `Blocked by: #<n>, #<n>` 一行。所有阻塞项关闭后，ticket 即解除阻塞。
- **前沿查询**：列出地图的开放子项（`gh issue list --state open`，限定在地图的 sub-issues / 任务列表内），排除拥有开放阻塞项（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中存在开放 issue）或已有 assignee 的项；按地图顺序的第一项胜出。
- **认领**：`gh issue edit <n> --add-assignee @me`——本次会话的第一次写操作。
- **解决**：`gh issue comment <n> --body "<answer>"`，然后 `gh issue close <n>`，最后向地图的 Decisions-so-far 追加上下文指针（gist + 链接）。

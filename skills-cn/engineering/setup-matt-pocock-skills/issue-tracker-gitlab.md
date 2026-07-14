# Issue 跟踪器：GitLab

此仓库的 issue 和 PRD 均以 GitLab issue 形式存在。所有操作均使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 约定


- **创建 issue**：`glab issue create --title "..." --description "..."`。多行描述使用 heredoc。传入 `--description -` 可打开编辑器。
- **读取 issue**：`glab issue view <number> --comments`。机器可读输出使用 `-F json`。
- **列出 issue**：使用带适当 `--label` 筛选的 `glab issue list -F json`。
- **评论 issue**：`glab issue note <number> --message "..."`。GitLab 将评论称为 “notes”。
- **添加 / 移除标签**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个标签可用逗号分隔，或重复传入该标志。
- **关闭**：`glab issue close <number>`。`glab issue close` 不接受关闭评论，因此先用 `glab issue note <number> --message "..."` 发布说明，再关闭。
- **合并请求**：GitLab 将 PR 称为 “merge requests”。使用 `glab mr create`、`glab mr view`、`glab mr note` 等命令；其结构与 `gh pr ...` 相同，只是以 `mr` 替代 `pr`，以 `note`/`--message` 替代 `comment`/`--body`。

从 `git remote -v` 推断仓库；在 clone 内运行时，`glab` 会自动完成此操作。

## 作为 triage 入口的合并请求

**MR 作为请求入口：no。** _（若本仓库将外部合并请求视为功能请求，则设为 `yes`；`/triage` 会读取此标志。）_

设为 `yes` 时，MR 使用 `glab mr` 的对应命令，经历与 issue 相同的标签和状态：


- **读取 MR**：使用 `glab mr view <number> --comments`；diff 使用 `glab mr diff <number>`。
- **列出待 triage 的外部 MR**：运行 `glab mr list -F json`，仅保留作者不是项目成员/所有者的 MR（贡献者的 MR，而非维护者进行中的工作）。
- **评论 / 添加标签 / 关闭**：使用 `glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

与 GitHub 不同，GitLab 对 issue 和 MR 分别编号，因此一旦知道维护者所指的载体，`#42` 就没有歧义。

## 当 skill 指示“发布到 issue 跟踪器”

创建 GitLab issue。

## 当 skill 指示“获取相关 ticket”

运行 `glab issue view <number> --comments`。

## Wayfinding 操作

供 `/wayfinder` 使用。**地图**是一个 issue，以**子** issue 作为 ticket。

- **地图**：带有 `wayfinder:map` 标签的单个 issue，正文包含 Notes / Decisions-so-far / Fog。使用 `glab issue create --label wayfinder:map`。（在支持原生 epic 的 GitLab 套餐中，可由 epic 承载地图；带标签的 issue 在任何环境都可用。）
- **子 ticket**：描述顶部带有 `Part of #<map>`，且标签为 `wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）的 issue。认领后，将 ticket 分配给推进工作的开发者。
- **阻塞**：使用 GitLab 的**原生阻塞链接**，这是权威且在 UI 中可见的表示。通过以 note 形式发布 `/blocked_by #<n>` 快捷操作来添加（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生阻塞链接是 Premium/Ultimate 功能；免费套餐（或不可用时）则回退为描述顶部的 `Blocked by: #<n>, #<n>` 一行。所有阻塞项关闭后，ticket 即解除阻塞。
- **前沿查询**：运行限定在地图子项内的 `glab issue list -F json`，排除拥有开放阻塞项（指向开放 issue 的原生 `blocked_by` 链接，使用 `glab api projects/:id/issues/:iid/links` 查询；或 `Blocked by` 行中存在开放 issue）或已有 assignee 的项；按地图顺序的第一项胜出。
- **认领**：`glab issue update <n> --assignee @me`——本次会话的第一次写操作。
- **解决**：`glab issue note <n> --message "<answer>"`，然后 `glab issue close <n>`，最后向地图的 Decisions-so-far 追加上下文指针（gist + 链接）。

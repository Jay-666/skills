# Issue 跟踪器：本地 Markdown

此仓库的 issue 与规格（也可称为 PRD）以 Markdown 文件形式存放于 `.scratch/`。

## 约定

- 每项功能一个目录：`.scratch/<feature-slug>/`
- 规格文件为 `.scratch/<feature-slug>/spec.md`
- 实现 issue 在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 中每个 ticket 一个文件，从 `01` 编号——绝不使用单个合并的 tickets 文件
- 每个 issue 文件顶部附近以 `Status:` 行记录 triage 状态（角色字符串见 `triage-labels.md`）
- 评论和对话历史追加在文件底部的 `## Comments` 标题下

## 当 skill 指示“发布到 issue 跟踪器”

在 `.scratch/<feature-slug>/` 下新建文件（必要时创建目录）。

## 当 skill 指示“获取相关 ticket”

读取所引用路径的文件。用户通常会直接传入路径或 issue 编号。

## Wayfinding 操作

供 `/wayfinder` 使用。**地图**是一个文件，每个 ticket 对应一个**子**文件。

- **地图**：`.scratch/<effort>/map.md`——包含 Notes / Decisions-so-far / Fog 的正文。
- **子 ticket**：`.scratch/<effort>/issues/NN-<slug>.md`，从 `01` 编号，正文中包含问题。`Type:` 行记录 ticket 类型（`research`/`prototype`/`grilling`/`task`）；`Status:` 行记录 `claimed`/`resolved`。
- **阻塞**：顶部附近的 `Blocked by: NN, NN` 行。所列的每个文件均为 `resolved` 时，ticket 才解除阻塞。
- **前沿**：扫描 `.scratch/<effort>/issues/`，查找开放、未阻塞且未认领的文件；编号最小者胜出。
- **认领**：在任何工作开始前，设置并保存 `Status: claimed`。
- **解决**：在 `## Answer` 标题下追加答案，设置 `Status: resolved`，然后在 `map.md` 中向地图的 Decisions-so-far 追加上下文指针（gist + 链接）。

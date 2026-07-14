---
name: setup-matt-pocock-skills
description: 为工程 skills 配置此仓库，设置 issue 跟踪器、分诊标签词汇和领域文档布局。在首次使用其他工程 skills 前运行一次。
disable-model-invocation: true
---

# 配置 Matt Pocock 的 Skills

搭建工程 skills 假定存在的仓库级配置：

- **Issue 跟踪器**：issues 存放的位置（默认 GitHub；开箱即支持本地 Markdown）
- **分诊标签**：五个规范分诊角色所使用的字符串
- **领域文档**：`CONTEXT.md` 和 ADR 所在位置，以及读取它们的使用规则

这是提示词驱动的 skill，而非确定性脚本。探索、呈现发现、与用户确认，然后写入。

## 流程

### 1. 探索

查看当前仓库以了解其起始状态。读取所有已有内容，不要假设：

- `git remote -v` 和 `.git/config`：这是 GitHub 仓库吗？是哪一个？
- 仓库根目录的 `AGENTS.md` 和 `CLAUDE.md`：其中任一个是否存在？其中是否已有 `## Agent skills` 章节？
- 仓库根目录的 `CONTEXT.md` 和 `CONTEXT-MAP.md`
- `docs/adr/` 和所有 `src/*/docs/adr/` 目录
- `docs/agents/`：是否已经存在此 skill 的先前输出？
- `.scratch/`：表明已使用本地 Markdown issue 跟踪器约定
- 是否安装 `triage` skill？（此 skill 旁的 `triage` skill 目录，或可用 skills 中的 `triage`。）这决定 B 节是否运行。
- 单体仓库信号：`pnpm-workspace.yaml`、package.json 中的 `workspaces` 字段，或包含自身 `src/` 的已填充 `packages/*`。这些仅存在于真正的大型多包仓库；缺失意味着单上下文，几乎每个仓库都是如此。

### 2. 呈现发现并询问

总结已存在和缺失的内容。然后按顺序处理各节，一节一个答案，再进入下一节。

以推荐答案引导每一节，使用户能用一个词接受它。仅当选项确实分叉时给出一行说明；当探索已确定答案时完全跳过该节（未安装 `triage` 时跳过 B 节，没有单体仓库时跳过 C 节）。

**A 节：Issue 跟踪器。**

> 说明：“issue 跟踪器”是此仓库 issues 存放的位置。`to-tickets`、`triage`、`to-spec` 和 `qa` 等 skills 都会从这里读取和写入，它们需要知道是调用 `gh issue create`、在 `.scratch/` 下写 Markdown 文件，还是遵循你描述的其他工作流。选择你实际跟踪本仓库工作的地方。

默认立场：这些 skills 为 GitHub 设计。若 `git remote` 指向 GitHub，则推荐它。若 `git remote` 指向 GitLab（`gitlab.com` 或自托管主机），推荐 GitLab。否则（或用户更喜欢时）提供：

- **GitHub**：issues 位于仓库的 GitHub Issues（使用 `gh` CLI）
- **GitLab**：issues 位于仓库的 GitLab Issues（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 Markdown**：issues 位于此仓库 `.scratch/<feature>/` 下的文件（适用于个人项目或没有远程仓库的仓库）
- **其他**（Jira、Linear 等）：请用户用一段话描述工作流；skill 将其记录为自由形式文本

将选择记录到 `docs/agents/issue-tracker.md`。GitHub 和 GitLab 模板带有“将 PR 视为请求入口”标志，默认**关闭**。保持关闭且不要提起它；希望将外部 PR 加入分诊队列的用户之后可在文件中开启该标志。

**B 节：分诊标签词汇。** 若未安装 `triage` skill（探索已告知），完全跳过本节，未安装的 skill 不需要标签。

若已安装，准确询问一个问题：

> 是否要保留默认分诊标签？（推荐：**是**）

默认值是五个规范角色，每个标签字符串等于其名称：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。用户回答**是**时按原样写入。仅当用户回答否，通常是因为其跟踪器已使用其他名称（例如用 `bug:triage` 表示 `needs-triage`）时，收集覆盖项，使 `triage` 应用已有标签而非创建重复项。

**C 节：领域文档。** 默认采用**单上下文**，即仓库根目录中的一个 `CONTEXT.md` 加 `docs/adr/`。这适合几乎每个仓库，直接写入，无需询问。

仅当探索发现单体仓库信号时，才提供**多上下文**，即根目录 `CONTEXT-MAP.md` 指向每个上下文的 `CONTEXT.md` 文件。然后确认其希望使用的布局。

### 3. 确认并编辑

向用户展示以下内容的草稿：

- 要添加到所编辑 `CLAUDE.md` 或 `AGENTS.md` 的 `## Agent skills` 块（选择规则见第 4 步）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 和 `docs/agents/triage-labels.md` 的内容（最后一项仅在安装 `triage` 时提供）

在写入前让用户编辑。

### 4. 写入

**选择要编辑的文件：**

- 若存在 `CLAUDE.md`，编辑它。
- 否则若存在 `AGENTS.md`，编辑它。
- 若两者均不存在，询问用户要创建哪一个，不要替他们选择。

当 `CLAUDE.md` 已存在时，绝不创建 `AGENTS.md`（反之亦然），始终编辑已有文件。

若选中文件中已有 `## Agent skills` 块，原地更新其内容而非追加重复块。不要覆盖用户对周围章节的编辑。

The block:

```markdown
## Agent 技能

### Issue 跟踪器

[issues 跟踪位置的一行摘要]。参见 `docs/agents/issue-tracker.md`。

### 分诊标签

[标签词汇的一行摘要]。参见 `docs/agents/triage-labels.md`。

### 领域文档

[布局的一行摘要，即“单上下文”或“多上下文”]。参见 `docs/agents/domain.md`。
```

仅当安装了 `triage` 且 B 节已运行时，才包含 `### Triage labels` 子块并写入 `docs/agents/triage-labels.md`。若未安装，两者均省略。

然后以此 skill 文件夹中的种子模板为起点写入文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub issue 跟踪器
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab issue 跟踪器
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 Markdown issue 跟踪器
- [triage-labels.md](./triage-labels.md)：标签映射（仅在安装 `triage` 时）
- [domain.md](./domain.md)：领域文档使用规则和布局

对于“其他” issue 跟踪器，使用用户描述从头编写 `docs/agents/issue-tracker.md`。

### 5. 完成

告知用户配置已完成，以及哪些工程 skills 将读取这些文件。说明他们之后可直接编辑 `docs/agents/*.md`，仅当希望切换 issue 跟踪器或从头重新开始时才需要重新运行此 skill。

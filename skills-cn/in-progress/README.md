# 开发中

仍在开发的 Skill，尚未准备发布——预计会有粗糙边角、破坏性变更和废弃实验。在进入稳定 bucket 前，它们不会纳入插件和顶层 README。

- **[loop-me](./loop-me/SKILL.md)** — 在多个会话中通过自我访谈形成可实现的工作流规格，以当前目录作为有状态工作区。用户调用。
- **[wizard](./wizard/SKILL.md)** — 生成交互式 bash 向导，引导人类完成手动流程（配置、一次性迁移、状态转换）——打开 URL、采集值、写入 `.env` 与 GitHub Actions secrets。用户调用。
- **[writing-beats](./writing-beats/SKILL.md)** — 将文章塑造成节拍组成的旅程，采用选择你自己的冒险式风格。选择起始节拍，只写该节拍，再转向下一个，直到文章自然结束。
- **[writing-fragments](./writing-fragments/SKILL.md)** — 访谈会话，从你身上采集片段——异质的写作素材——并将其追加到一个文档中，作为未来文章的原始材料。
- **[writing-shape](./writing-shape/SKILL.md)** — 获取一份包含原始材料的 Markdown 文件，逐段塑造成文章，并在每一步讨论格式选择。
- **[claude-handoff](./claude-handoff/SKILL.md)** — 将当前对话交给立即接手工作的全新后台 Agent，通过 `claude --bg` 传入交接摘要。用户调用。
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)** — 将 dependency-cruiser 接入 TypeScript 仓库，使每个 package 成为深模块——实现隐藏在子文件夹中，只能通过入口文件访问，测试也通过这些入口进行。用户调用。

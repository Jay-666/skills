# 工程

日常用于代码工作的 Skill。

## 用户调用

仅在手动输入名称时可调用（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)** — 询问适合当前情况的 skill 或流程。本仓库用户调用 Skill 的路由器。
- **[grill-with-docs](./grill-with-docs/SKILL.md)** — 访谈会话，同时构建项目的领域模型、提炼术语，并内联更新 `CONTEXT.md` 和 ADR。
- **[triage](./triage/SKILL.md)** — 让 issue 在 triage 角色组成的状态机中流转。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)** — 扫描代码库中的深化机会，以可视化 HTML 报告呈现，再对所选机会进行访谈。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)** — 为工程类 Skill 配置仓库（issue 跟踪器、triage 标签、领域文档布局）。每个仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)** — 将当前对话转为规格并发布到 issue 跟踪器。
- **[to-tickets](./to-tickets/SKILL.md)** — 将计划、规格或对话拆分为一组 tracer-bullet ticket，并在每项中声明阻塞边——写入本地文件，或使用真实跟踪器的原生阻塞链接。
- **[implement](./implement/SKILL.md)** — 构建规格或 ticket 集合描述的工作，在预先约定的接缝处驱动 `/tdd`，并在提交前以 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)** — 将超过单次 Agent 会话容量的大型工作规划为 issue 跟踪器中共享的决策 ticket 地图，逐项解决，直至抵达目标的路径清晰。

## 模型调用

模型或用户均可调用（描述包含丰富的触发措辞，便于模型主动使用）。

- **[prototype](./prototype/SKILL.md)** — 构建一次性原型来回答设计问题：用于状态/逻辑的可运行终端应用，或多个可切换的 UI 变体。

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)** — 针对棘手 bug 与性能回归的纪律化诊断循环：复现 → 最小化 → 提出假设 → 加入观测 → 修复 → 回归测试。
- **[research](./research/SKILL.md)** — 基于高度可信的一手来源调查问题，并将带引用的发现保存为仓库中的 Markdown 文件，以后台 Agent 运行。
- **[tdd](./tdd/SKILL.md)** — 使用红-绿-重构循环进行测试驱动开发。每次构建功能或修复 bug 的一个垂直切片。
- **[domain-modeling](./domain-modeling/SKILL.md)** — 主动构建并打磨项目领域模型：质询术语、用场景压力测试，并内联更新 `CONTEXT.md` 和 ADR。
- **[codebase-design](./codebase-design/SKILL.md)** — 设计深模块的共同规范与词汇：小接口、干净接缝、可通过接口测试。
- **[code-review](./code-review/SKILL.md)** — 对固定点之后的 diff 进行双轴审查：**Standards**（是否遵循仓库编码规范及 Fowler 异味基线？）与 **Spec**（是否忠实实现来源 issue/PRD？），由并行子 Agent 执行。
- **[resolving-merge-conflicts](./resolving-merge-conflicts/SKILL.md)** — 逐块处理进行中的 git merge 或 rebase 冲突，通过追溯双方一手来源的意图进行解决，随后完成操作——绝不使用 `--abort`。

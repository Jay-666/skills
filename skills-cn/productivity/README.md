# 效率

通用工作流工具，不限于代码。

## 用户调用

仅在手动输入名称时可调用（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)** — 围绕计划或设计持续接受访谈，直至决策树的每个分支均得到解决。
- **[handoff](./handoff/SKILL.md)** — 将当前对话压缩为交接文档，使另一名 Agent 能继续工作。
- **[teach](./teach/SKILL.md)** — 利用当前目录作为有状态教学工作区，在多个会话中教授用户新技能或概念。
- **[writing-great-skills](./writing-great-skills/SKILL.md)** — 编写和编辑 Skill 的参考：让 Skill 可预测的词汇与原则。

## 模型调用

模型或用户均可调用（描述包含丰富的触发措辞，便于模型主动使用）。

- **[grilling](./grilling/SKILL.md)** — 围绕计划、决策或想法持续访谈用户，直至决策树的每个分支均得到解决。

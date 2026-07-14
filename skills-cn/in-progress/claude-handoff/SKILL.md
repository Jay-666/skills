---
name: claude-handoff
description: 将当前对话交接给一个新的后台 agent，使其立即继续工作。
argument-hint: "下一次会话将用于什么工作？"
disable-model-invocation: true
---

编写当前对话的交接摘要，使新的 agent 能继续工作。不要保存摘要，而是以该摘要作为提示词启动一个后台 agent：`claude --bg --name "<descriptive name>" "<handoff summary>"`。它会在当前工作目录启动并立即返回；用户使用 `claude agents` 管理它。

始终传入带描述性名称的 `-n`/`--name`（例如 `--name "Fix login bug"`），它会设置任务列表、会话选择器和终端标题中显示的名称。

在摘要中包含“建议使用的 skills”章节，列出该 agent 应调用的 skills。

不要重复其他产物（PRD、计划、ADR、issue、提交、差异）中已有的内容，改为通过路径或 URL 引用它们。

删去所有敏感信息，例如 API 密钥、密码或个人身份信息，因为该摘要会成为 agent 的提示词。

若用户传入了参数，将其视为下一次会话的工作重点说明，并据此调整摘要。

---
name: handoff
description: 将当前对话压缩为交接文档，供另一名 Agent 接手。
argument-hint: "下一次会话将用于什么？"
disable-model-invocation: true
---

编写总结当前对话的交接文档，使新的 Agent 可以继续工作。将其保存到用户操作系统的临时目录，而非当前工作区。

在文档中加入“建议使用的技能”章节，列出 Agent 应调用的 Skill。

不要重复其他产物（规格、计划、ADR、issue、commit、diff）中已记录的内容；应以路径或 URL 引用它们。

遮蔽 API key、密码或个人身份信息等敏感数据。

若用户传入参数，将其视为下一次会话关注内容的描述，并据此调整文档。

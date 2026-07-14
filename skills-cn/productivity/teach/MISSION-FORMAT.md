# MISSION.md 格式

`MISSION.md` 位于工作区根目录，记录用户学习该主题的*原因*。每个教学决策——接下来教什么、呈现哪些资源、设计哪些练习——都应回溯到此文档。

## 模板

```md
# Mission: {Topic}

## Why
{1-3 sentences. The concrete real-world goal the user is chasing. What changes in their life or work when they have this skill? Avoid abstract framings like "to understand X" — push for the underlying outcome.}

## Success looks like
- {A specific, observable thing the user will be able to do}
- {Another specific thing}
- {…}

## Constraints
- {Time, budget, prior commitments, learning preferences, anything that bounds the approach}

## Out of scope
- {Adjacent topics the user explicitly does not want to chase right now — protects the zone of proximal development}
```

## 规则

- **每个工作区一项任务。** 若用户要学习两件不相关的事，则应使用两个工作区。
- **具体优于抽象。**“十月前跑完半程马拉松”优于“变得更健康”；“向团队交付一个 Rust CLI”优于“学习 Rust”。
- **质询模糊表达。** 若用户无法说明原因，应先访谈再写入。糟糕的任务不如没有任务。
- **现实变化时修订。** 任务会变化。用户的目标转移时，更新此文件，不要让过时任务引导未来会话。
- **保持简短。** `MISSION.md` 若超过一屏，就已不再是指南针，而开始成为计划。

---
name: scaffold-exercises
description: 创建包含章节、题目、解答和讲解的练习目录结构，并确保可通过 lint。适用于用户希望搭建练习、创建练习桩代码，或设置新的课程章节时。
---

# 搭建练习框架

创建能通过 `pnpm ai-hero-cli internal lint` 的练习目录结构，然后使用 `git commit` 提交。

## 目录命名

- **章节**：`exercises/` 内的 `XX-section-name/`（例如 `01-retrieval-skill-building`）
- **练习**：章节内的 `XX.YY-exercise-name/`（例如 `01.03-retrieval-with-bm25`）
- 章节编号为 `XX`，练习编号为 `XX.YY`
- 名称使用短横线命名法（小写字母和连字符）

## 练习变体

每项练习至少需要以下一个子目录：

- `problem/`：包含 TODO 的学生工作区
- `solution/`：参考实现
- `explainer/`：概念材料，不含 TODO

创建桩内容时，除非计划另有规定，默认使用 `explainer/`。

## 必需文件

每个子目录（`problem/`、`solution/`、`explainer/`）都需要一个 `readme.md`，且该文件：

- **不能为空**（必须有实际内容，即使仅有一行标题也可以）
- 不包含失效链接

创建桩内容时，创建只包含标题和描述的最小 readme：

```md
# 练习标题

在此填写描述
```

如果子目录包含代码，还需要 `main.ts`（超过 1 行）。但对于桩内容，仅包含 readme 的练习即可。

## 工作流

1. **解析计划**：提取章节名称、练习名称和变体类型
2. **创建目录**：为每个路径执行 `mkdir -p`
3. **创建桩 readme**：每个变体目录创建一个含标题的 `readme.md`
4. **运行 lint**：执行 `pnpm ai-hero-cli internal lint` 验证
5. **修复错误**：反复处理直至 lint 通过

## Lint 规则摘要

检查器（`pnpm ai-hero-cli internal lint`）会检查：

- 每项练习都有子目录（`problem/`、`solution/`、`explainer/`）
- 至少存在 `problem/`、`explainer/` 或 `explainer.1/` 中的一个
- 主子目录中存在非空的 `readme.md`
- 不存在 `.gitkeep` 文件
- 不存在 `speaker-notes.md` 文件
- readme 中没有失效链接
- readme 中没有 `pnpm run exercise` 命令
- 除非仅含 readme，否则每个子目录都需要 `main.ts`

## 移动或重命名练习

重新编号或移动练习时：

1. 使用 `git mv`（而非 `mv`）重命名目录，以保留 git 历史
2. 更新数字前缀以维持顺序
3. 移动后重新运行 lint

示例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 示例：根据计划创建桩内容

给定如下计划：

```
第 05 章：记忆 Skill 构建
- 05.01 记忆简介
- 05.02 短期记忆（explainer + problem + solution）
- 05.03 长期记忆
```

创建：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

随后创建 readme 桩文件：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```

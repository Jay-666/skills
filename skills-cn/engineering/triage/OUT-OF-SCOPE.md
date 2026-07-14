# 范围外知识库

仓库中的 `.out-of-scope/` 目录保存被拒绝功能请求的持久记录，作用有二：

1. **组织记忆**——记录功能被拒绝的原因，避免 issue 关闭后理由随之丢失
2. **去重**——新 issue 与既往拒绝相符时，skill 可呈现先前决定，而无需重新争论

## 目录结构

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

每个**概念**一个文件，而非每个 issue 一个文件。请求相同功能的多个 issue 归入同一文件。

## 文件格式

文件应采用轻松、易读的风格，更像简短的设计文档而非数据库条目。使用段落、代码样例和示例，让初次接触的人也能清楚理解其中的理由。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42 — "Add dark mode support"
- #87 — "Night theme for accessibility"
- #134 — "Dark theme option"
```

### 文件命名

概念使用简短、描述性的 kebab-case 名称，如 `dark-mode.md`、`plugin-system.md`、`graphql-api.md`。名称应足够直观，让浏览目录的人无需打开文件便能知道被拒绝的内容。

### 撰写理由

理由应当充实：不是“我们不想要”，而是说明原因。好的理由会涉及：

- 项目范围或理念（“本项目专注于 X；主题是下游关注点”）
- 技术约束（“支持它需要 Y，这与我们的 Z 架构冲突”）
- 战略决策（“我们选择 A 而非 B，因为……”）

理由应具有耐久性。避免引用临时情况（“我们现在太忙了”），那并非真正的拒绝，而是延期。

## 何时检查 `.out-of-scope/`

在 triage 期间（第 1 步：收集上下文），读取 `.out-of-scope/` 中的全部文件。评估新 issue 时：

- 检查请求是否匹配已有的范围外概念
- 按概念相似性匹配，而不是按关键词——“夜间主题”匹配 `dark-mode.md`
- 若有匹配，向维护者提示：“此请求与 `.out-of-scope/dark-mode.md` 相似——我们此前因[理由]而拒绝。你仍然这样认为吗？”

维护者可以：

- **确认**——将新 issue 加入现有文件的 “Prior requests” 列表，然后关闭
- **重新考虑**——删除或更新范围外文件，issue 按正常 triage 流程处理
- **不同意**——两个 issue 相关但不同，按正常 triage 流程处理

## 何时写入 `.out-of-scope/`

仅当 **enhancement**（而非 bug）被*拒绝*并标记为 `wontfix` 时写入。此规则对 enhancement PR 与 issue 完全相同——记录被拒绝的 PR，避免同一请求作为新代码再次出现。

当某项内容因**已实现**而以 `wontfix` 关闭时，**不要**在此写入。那是已交付的功能而非被拒绝的功能；记录它会以错误拒绝污染去重检查。应在关闭评论中指出该功能现存的位置。

流程：

1. 维护者决定某功能请求不在范围内
2. 检查是否已有匹配的 `.out-of-scope/` 文件
3. 若有：将新 issue 追加到 “Prior requests” 列表
4. 若无：创建文件，包含概念名称、决定、理由和首个既往请求
5. 在 issue 中发布评论，说明决定并提及 `.out-of-scope/` 文件
6. 使用 `wontfix` 标签关闭 issue

## 更新或移除范围外文件

维护者改变对先前被拒绝概念的看法时：

- 删除 `.out-of-scope/` 文件
- skill 无需重新打开旧 issue——它们是历史记录
- 触发重新考虑的新 issue 按正常 triage 流程处理

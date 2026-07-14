---
name: obsidian-vault
description: 在含有 wikilink 和索引笔记的 Obsidian vault 中搜索、创建和管理笔记。适用于用户希望在 Obsidian 中查找、创建或整理笔记时。
---

# Obsidian 知识库

## 知识库位置

`/mnt/d/Obsidian Vault/AI Research/`

根目录大多采用平铺结构。

## 命名约定

- **索引笔记**：汇总相关主题（如 `Ralph Wiggum Index.md`、`Skills Index.md`、`RAG Index.md`）
- 所有笔记名使用 **Title Case**
- 不使用文件夹组织，改用链接和索引笔记

## 链接

- 使用 Obsidian `[[wikilinks]]` 语法：`[[Note Title]]`
- 笔记在底部链接依赖项/相关笔记
- 索引笔记只是 `[[wikilinks]]` 的列表

## 工作流

### 搜索笔记

```bash
# Search by filename
find "/mnt/d/Obsidian Vault/AI Research/" -name "*.md" | grep -i "keyword"

# Search by content
grep -rl "keyword" "/mnt/d/Obsidian Vault/AI Research/" --include="*.md"
```

也可直接在知识库路径上使用 Grep/Glob 工具。

### 创建新笔记

1. 文件名使用 **Title Case**
2. 按知识库规则，将内容写成一个学习单元
3. 在底部向相关笔记添加 `[[wikilinks]]`
4. 若属于编号序列，使用层级编号方案

### 查找相关笔记

在整个知识库中搜索 `[[Note Title]]` 以查找反向链接：

```bash
grep -rl "\\[\\[Note Title\\]\\]" "/mnt/d/Obsidian Vault/AI Research/"
```

### 查找索引笔记

```bash
find "/mnt/d/Obsidian Vault/AI Research/" -name "*Index*"
```

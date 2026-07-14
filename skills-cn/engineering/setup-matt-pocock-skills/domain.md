# 领域文档

工程类 skill 在探索代码库时应如何使用本仓库的领域文档。

## 探索前请阅读

- 仓库根目录的 **`CONTEXT.md`**，或
- 若存在，仓库根目录的 **`CONTEXT-MAP.md`**——它为每个 context 指向一个 `CONTEXT.md`。读取与主题相关的每一份文件。
- **`docs/adr/`**——阅读涉及即将处理区域的 ADR。在多 context 仓库中，还应检查 `src/<context>/docs/adr/` 下的 context 范围决策。

若其中任何文件不存在，**静默继续**。不要标记其缺失，也不要预先建议创建它们。仅当术语或决策实际得到解决时，`/domain-modeling` skill（通过 `/grill-with-docs` 和 `/improve-codebase-architecture` 调用）才会按需创建它们。

## 文件结构

单 context 仓库（多数仓库）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多 context 仓库（根目录存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用术语表的词汇

当输出中为领域概念命名（issue 标题、重构提案、假设、测试名称）时，使用 `CONTEXT.md` 中定义的术语。不要偏离到术语表明确避免使用的同义词。

若需要的概念尚未出现在术语表中，这是一项信号：要么正在创造项目不用的语言（应重新考虑），要么存在真正的缺口（为 `/domain-modeling` 记录它）。

## 标记 ADR 冲突

若输出与现有 ADR 相矛盾，应明确指出，而不是静默覆盖：

> _与 ADR-0007（event-sourced orders）冲突——但值得重新讨论，因为……_

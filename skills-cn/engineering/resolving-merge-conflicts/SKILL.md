---
name: resolving-merge-conflicts
description: "需要解决进行中的 git merge/rebase 冲突时使用。"
---

1. **查看当前状态**。检查 merge/rebase 的 git 历史和冲突文件。

2. **找到每个冲突的一手来源**。深入理解每项改动为何作出，以及其原始意图。阅读 commit message，检查 PR 和原始 issue/ticket。

3. **解决每个 hunk。** 尽可能保留双方意图；若不兼容，选择符合该 merge 明确目标的一方，并注明取舍。**不要**创造新行为。始终解决，绝不 `--abort`。

4. 发现并运行项目的**自动检查**——通常依次为 typecheck、测试、format。修复 merge 造成的所有问题。

5. **完成 merge/rebase。** 暂存全部内容并提交。若在 rebase，持续执行 rebase 流程，直至所有 commit 均完成 rebase。

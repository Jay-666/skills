---
name: setup-ts-deep-modules
description: 将 dependency-cruiser 接入 TypeScript 仓库，使每个包成为深模块：实现隐藏在子目录中，仅能通过入口文件访问。由用户调用。
disable-model-invocation: true
---

# 配置 TS 深模块

使本仓库中的每个包成为**深模块**：在小接口后隐藏大量行为。包的公共表面是其**入口点**，即包根目录中的文件，其子目录中的一切均隐藏。此 skill 安装 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 及使入口点成为唯一访问路径的规则，然后证明规则确实生效。

有关词汇（深模块、接口、边界、深度），运行 `/codebase-design` skill，并始终使用其语言。

## 要强制的形态

```
src/packages/
  <name>/
    index.ts        ← 一个入口点（公开）。从外部导入此文件。
    client.ts       ← 另一个入口点。包可暴露多个入口点。
    lib/            ← 实现：从外部隐藏，内部可自由相互导入。
    tests/          ← 同目录测试和 fixture（子目录，因此私有）。
```

公共表面是包的**根文件**，而非一个指定的 `index.ts`。约定上实现位于 `lib/`，测试位于 `tests/`，使每个包都具有相同的双目录形态。但规则本身是通用的：*任何*子目录中的*任何*内容均为私有，因此永远无需为新增目录扩展配置。

四条规则，均为 `error`：

1. **入口点边界**：包外部代码（应用代码或其他包）仅可导入该包的入口点（根文件），绝不可导入其子目录中的内容。
2. **包内自由**：包自身文件可自由相互导入。
3. **测试经由入口点**：`<pkg>/tests/` 下的文件可导入任意包的入口点及自身 `tests/` fixture，但绝不可导入任意包子目录内部内容（包括自己的）。允许跨包集成测试，不允许深层导入。
4. **无循环**：不存在依赖循环。

**入口点，不是 barrel。** 因为公共表面是*每个*根文件，包可暴露多个小入口点（`index.ts`、`client.ts`、`server.ts`），而非把一切汇集到一个巨大的 `index.ts`。不鼓励重新导出整棵子树的 barrel 文件，应保持入口点小巧，并将实现隐藏于子目录。

分层（哪些包可依赖哪些包）是*不同的*关注点，在配置中保留为注释 stub 供本仓库填写。

## 步骤

### 1. 检测环境

- **包管理器**：`pnpm-lock.yaml` 对应 pnpm、`yarn.lock` 对应 yarn、`bun.lockb` 对应 bun，否则 npm。对下方每个命令使用它（`pnpm`、`yarn`、`npm run` 或 `bunx`）。
- **包根目录**：若存在 `src/` 则使用 `src/packages`，否则使用 `packages`。若仓库已有其他明显约定，与用户确认选择。
- **现有配置**：检查 `.dependency-cruiser.*` 文件。若存在，**不要**覆盖它；合并四条规则和选项，并告知用户添加了什么。

**完成标准：** 已知包管理器、包根目录和现有配置状态。

### 2. 安装 dependency-cruiser

使用检测到的包管理器将 `dependency-cruiser` 安装为开发依赖。

**完成标准：** `dependency-cruiser` 位于 `devDependencies` 中。

### 3. 编写配置

将 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 复制到仓库根目录并命名为 `.dependency-cruiser.cjs`。将 `PACKAGES_ROOT` 设为第 1 步检测到的根目录。规则基于路径深度且与扩展名无关，因此无需其他调整。

**完成标准：** 存在正确 `PACKAGES_ROOT` 的 `.dependency-cruiser.cjs`，且四条禁止规则均存在。

### 4. 接入检查

- 添加 `lint:boundaries` 脚本：`depcruise <packages-root>`（或 `depcruise src`）。
- 将其纳入仓库的总检查命令，即已经运行类型检查的命令（如 `check`、`ci` 或 `validate` 脚本）。**不要**触碰 `tsconfig` 或添加路径别名。
- 若没有总检查脚本，添加 `lint:boundaries` 并告知用户将其纳入 CI。

**完成标准：** 存在 `lint:boundaries`，并作为与类型检查相同命令的一部分运行。

### 5. 搭建示例包

创建已提交的 `<packages-root>/example/`，作为可复制模板：

- `index.ts`：一个入口点。导出一个委托给内部文件的函数（使包可见地*深*，而非简单转发）。
- `lib/impl.ts`：位于**子目录**的内部文件，由 `index.ts` 导入，无法从外部访问。
- `tests/example.test.ts`：**仅**导入 `../index`（一个入口点），并对公共函数进行断言。

告知用户这是可复制或删除的起步模板。

**完成标准：** 示例包存在，通过根入口点暴露其行为，并在子目录中隐藏 `impl`。

### 6. 证明规则生效

这是整个 skill 的完成标准：不在违规时失败的配置毫无价值。

1. 运行 `lint:boundaries`，它必须在干净的示例上**通过**。
2. 暂时向 `tests/example.test.ts` 添加深层导入（例如 `import { thing } from "../lib/impl"`）。再次运行 `lint:boundaries`，它必须以 `tests-through-entrypoints` **失败**。
3. 恢复深层导入。再运行一次，必须**通过**。

**完成标准：** 已观察到一次通过、深层导入时一次失败、恢复后再次通过。若第 2 步未失败，规则接入不正确，必须在结束前修复。

### 7. 记录约定

在**包目录中**（`<packages-root>/README.md`）编写 `README.md`，与它约束的包相邻。内容包括：`src/packages/<name>/` 布局（根目录放入口点、`lib/` 放实现、`tests/` 放测试），“仅通过包的入口点（根文件）导入”，以及如何运行 `lint:boundaries`。明确**不鼓励 barrel 文件**：暴露多个小入口点，而不是通过一个 index 重新导出整棵子树。内容保持为可复制片段加上每条规则一段。

然后从仓库的 agent 指令文件添加指向它的**上下文指针**：若存在则为 `CLAUDE.md`，否则为 `AGENTS.md`（若两者都不存在则创建 `AGENTS.md`）。一行即可，例如 `Packages are deep modules — see [src/packages/README.md](./src/packages/README.md) before adding or importing one.` 这使 agent 能发现边界规则，而非触犯它。

**完成标准：** 存在 `<packages-root>/README.md` 且其中不鼓励 barrel，仓库的 `CLAUDE.md` 或 `AGENTS.md` 链接到它。

## 注意事项

- 配置中的 `$1` 反向引用（dependency-cruiser 的组匹配）允许包访问自身内部而外部不能访问。不要将它们压平为每包单独规则。
- 公共与私有由**深度**决定：包的根文件是入口点，任何子目录中的内容均为私有。约定子目录是 `lib/`（实现）和 `tests/`，但规则并未硬编码它们，任意子目录均为私有，因此新目录永远无需更改配置。添加入口点只需添加根文件，无需 barrel。
- 包是**扁平的**：根下只有一层直接子项。包的内部可任意深层嵌套，但包不可包含另一个包。
- 使用 `.cjs`（而非 `.js`），以便配置的 `module.exports` 即使在 `"type": "module"` 仓库中也能工作。

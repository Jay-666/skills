---
name: setup-pre-commit
description: 在当前仓库中配置包含 lint-staged（Prettier）、类型检查与测试的 Husky 提交前钩子。适用于用户希望添加提交前钩子、设置 Husky、配置 lint-staged，或在提交时执行格式化、类型检查和测试时。
---

# 配置提交前钩子

## 将配置的内容

- **Husky** 提交前钩子
- 对所有暂存文件运行 Prettier 的 **lint-staged**
- **Prettier** 配置（若不存在）
- 在提交前钩子中运行的 **typecheck** 和 **test** 脚本

## 步骤

### 1. 检测包管理器

检查 `package-lock.json`（npm）、`pnpm-lock.yaml`（pnpm）、`yarn.lock`（yarn）和 `bun.lockb`（bun）。使用实际存在的包管理器；无法确定时默认使用 npm。

### 2. 安装依赖

将下列包安装为开发依赖：

```
husky lint-staged prettier
```

### 3. 初始化 Husky

```bash
npx husky init
```

这会创建 `.husky/` 目录，并在 package.json 中添加 `prepare: "husky"`。

### 4. 创建 `.husky/pre-commit`

写入此文件（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**调整**：将 `npm` 替换为检测到的包管理器。若仓库的 package.json 中没有 `typecheck` 或 `test` 脚本，省略对应行并告知用户。

### 5. 创建 `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. 创建 `.prettierrc`（若不存在）

仅在不存在 Prettier 配置时创建。使用以下默认值：

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. 验证

- [ ] `.husky/pre-commit` 存在且可执行
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` 脚本为 `"husky"`
- [ ] `prettier` 配置存在
- [ ] 运行 `npx lint-staged` 验证其工作正常

### 8. 提交

暂存所有已修改或新建的文件，并使用提交信息：`Add pre-commit hooks (husky + lint-staged + prettier)`。

这会运行新的提交前钩子，是验证所有内容正常工作的良好冒烟测试。

## 注意事项

- Husky v9+ 的钩子文件不需要 shebang
- `prettier --ignore-unknown` 会跳过 Prettier 无法解析的文件（图片等）
- 提交前钩子先运行 lint-staged（快速且只处理暂存文件），再运行完整类型检查和测试

---
name: git-guardrails-claude-code
description: 配置 Claude Code 钩子，在危险 git 命令（push、reset --hard、clean、branch -D 等）执行前将其阻止。适用于用户希望防止破坏性 git 操作、添加 git 安全钩子，或在 Claude Code 中阻止 git push/reset 时。
---

# 配置 Git 防护

配置一个 PreToolUse 钩子，在 Claude 执行危险 git 命令前拦截并阻止它们。

## 会被阻止的命令

- `git push` (all variants including `--force`)
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

被阻止时，Claude 会看到一条提示，说明它没有执行这些命令的权限。

## 步骤

### 1. 询问范围

询问用户：仅为**当前项目**（`.claude/settings.json`）安装，还是为**所有项目**（`~/.claude/settings.json`）安装？

### 2. 复制钩子脚本

随附脚本位于：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

根据范围复制到目标位置：

- **项目**：`.claude/hooks/block-dangerous-git.sh`
- **全局**：`~/.claude/hooks/block-dangerous-git.sh`

使用 `chmod +x` 使其可执行。

### 3. 将钩子添加到设置

添加到相应的设置文件：

**项目**（`.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**全局**（`~/.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

若设置文件已存在，将该钩子合并到现有的 `hooks.PreToolUse` 数组中，不要覆盖其他设置。

### 4. 询问自定义需求

询问用户是否希望从阻止列表中添加或移除模式。根据回答编辑复制出的脚本。

### 5. 验证

运行快速测试：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

应以退出码 2 退出，并向 stderr 输出 BLOCKED 消息。

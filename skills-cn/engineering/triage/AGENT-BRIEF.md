# 编写 Agent 简报

Agent 简报是在 GitHub issue 或 PR 转为 `ready-for-agent` 时发布的结构化评论。它是离线 Agent 据以工作的权威规格。原始正文和讨论只是上下文，Agent 简报才是约定。

简报说明 **Agent 应做什么**，涵盖两种载体：对于 issue，即从零实现改动；对于 PR，即针对*现有 diff*尚未完成的工作——完成它、补齐缺口、处理审查意见。两者遵循相同原则；下方 PR 示例展示了差别。

## 原则

### 耐久性优于精确定位

issue 可能在 `ready-for-agent` 停留数天或数周，其间代码库会变化。应编写即使文件重命名、移动或重构后仍然有用的简报。

- **应当**描述接口、类型和行为契约
- **应当**指明 Agent 需要查找或修改的具体类型、函数签名或配置形状
- **不要**引用文件路径——它们会过时
- **不要**引用行号
- **不要**假定当前的实现结构会保持不变

### 描述行为，不描述步骤

描述系统**应当做什么**，而不是**如何**实现。Agent 会重新探索代码库并自行做出实现决策。

- **好：**“`SkillConfig` 类型应接受一个类型为 `CronExpression` 的可选 `schedule` 字段”
- **差：**“打开 src/types/skill.ts，在第 42 行添加 schedule 字段”
- **好：**“用户不带参数运行 `/triage` 时，应看到需要关注的 issue 摘要”
- **差：**“在主处理函数中添加 switch 语句”

### 完整的验收标准

Agent 需要知道何时完成。每份 Agent 简报都必须给出具体、可测试的验收标准，每项标准均应可独立验证。

- **好：**“运行 `gh issue list --label needs-triage` 会返回已完成初步分类的 issue”
- **差：**“Triage 应能正确工作”

### 明确的范围边界

说明哪些内容不在范围内。这能防止 Agent 过度实现，或对相邻功能作出假设。

## Template

```markdown
## Agent Brief

**Category:** bug / enhancement
**Summary:** one-line description of what needs to happen

**Current behavior:**
Describe what happens now. For bugs, this is the broken behavior.
For enhancements, this is the status quo the feature builds on.

**Desired behavior:**
Describe what should happen after the agent's work is complete.
Be specific about edge cases and error conditions.

**Key interfaces:**
- `TypeName` — what needs to change and why
- `functionName()` return type — what it currently returns vs what it should return
- Config shape — any new configuration options needed

**Acceptance criteria:**
- [ ] Specific, testable criterion 1
- [ ] Specific, testable criterion 2
- [ ] Specific, testable criterion 3

**Out of scope:**
- Thing that should NOT be changed or addressed in this issue
- Adjacent feature that might seem related but is separate
```

## 示例

### 好的 Agent 简报（bug）

```markdown
## Agent Brief

**Category:** bug
**Summary:** Skill description truncation drops mid-word, producing broken output

**Current behavior:**
When a skill description exceeds 1024 characters, it is truncated at exactly
1024 characters regardless of word boundaries. This produces descriptions
that end mid-word (e.g. "Use when the user wants to confi").

**Desired behavior:**
Truncation should break at the last word boundary before 1024 characters
and append "..." to indicate truncation.

**Key interfaces:**
- The `SkillMetadata` type's `description` field — no type change needed,
  but the validation/processing logic that populates it needs to respect
  word boundaries
- Any function that reads SKILL.md frontmatter and extracts the description

**Acceptance criteria:**
- [ ] Descriptions under 1024 chars are unchanged
- [ ] Descriptions over 1024 chars are truncated at the last word boundary
      before 1024 chars
- [ ] Truncated descriptions end with "..."
- [ ] The total length including "..." does not exceed 1024 chars

**Out of scope:**
- Changing the 1024 char limit itself
- Multi-line description support
```

### 好的 Agent 简报（enhancement）

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Add `.out-of-scope/` directory support for tracking rejected feature requests

**Current behavior:**
When a feature request is rejected, the issue is closed with a `wontfix` label
and a comment. There is no persistent record of the decision or reasoning.
Future similar requests require the maintainer to recall or search for the
prior discussion.

**Desired behavior:**
Rejected feature requests should be documented in `.out-of-scope/<concept>.md`
files that capture the decision, reasoning, and links to all issues that
requested the feature. When triaging new issues, these files should be
checked for matches.

**Key interfaces:**
- Markdown file format in `.out-of-scope/` — each file should have a
  `# Concept Name` heading, a `**Decision:**` line, a `**Reason:**` line,
  and a `**Prior requests:**` list with issue links
- The triage workflow should read all `.out-of-scope/*.md` files early
  and match incoming issues against them by concept similarity

**Acceptance criteria:**
- [ ] Closing a feature as wontfix creates/updates a file in `.out-of-scope/`
- [ ] The file includes the decision, reasoning, and link to the closed issue
- [ ] If a matching `.out-of-scope/` file already exists, the new issue is
      appended to its "Prior requests" list rather than creating a duplicate
- [ ] During triage, existing `.out-of-scope/` files are checked and surfaced
      when a new issue matches a prior rejection

**Out of scope:**
- Automated matching (human confirms the match)
- Reopening previously rejected features
- Bug reports (only enhancement rejections go to `.out-of-scope/`)
```

### 好的 Agent 简报（PR）

对于 PR，“Current behavior”描述的是 diff 的状态，简报应要求 Agent 完成或修复它，而不是从零构建。

```markdown
## Agent Brief

**Category:** enhancement
**Summary:** Finish the contributor's `--json` output flag for `triage list`

**Current behavior:**
The PR adds a `--json` flag that serializes the issue list to JSON. The happy
path works and the diff matches the project's command structure. Two gaps
remain: errors are still printed as human text (not JSON), and the new flag has
no test coverage.

**Desired behavior:**
With `--json`, all output — including errors — is well-formed JSON on stdout,
and the command's exit codes are unchanged. The existing human-readable output
is untouched when the flag is absent.

**Key interfaces:**
- The command's error path should emit `{ "error": string }` under `--json`
  instead of the plain-text error
- Reuse the existing serializer the PR already added; don't introduce a second

**Acceptance criteria:**
- [ ] `triage list --json` emits valid JSON for both success and error cases
- [ ] Exit codes match the non-JSON command
- [ ] A test covers the `--json` success output and one error case
- [ ] Default (non-JSON) output is byte-for-byte unchanged

**Out of scope:**
- Adding `--json` to any other command
- Changing the JSON shape of the success payload the PR already defined
```

### 不好的 Agent 简报

```markdown
## Agent Brief

**Summary:** Fix the triage bug

**What to do:**
The triage thing is broken. Look at the main file and fix it.
The function around line 150 has the issue.

**Files to change:**
- src/triage/handler.ts (line 150)
- src/types.ts (line 42)
```

它不好的原因：
- 没有类别
- 描述含糊（“the triage thing is broken”）
- 引用了会过时的文件路径和行号
- 没有验收标准
- 没有范围边界
- 没有说明当前行为与期望行为

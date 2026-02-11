---
name: git-commit
description: 安全且一致的 Git 提交。当用户要求“commit”或“提交”时使用。同时需避免意外泄露密钥或包含无用的差异。
---

# Git Commit

## 概述

以最低风险创建高质量提交：审查更改、有意识地暂存、编写清晰的信息，并根据具体情况选择正确的 Git 选项。

## 默认防护机制

优先选择小型原子化提交：易于评审，且在出问题时易于回滚。

除非用户明确要求，否则不运行测试或编译。

严禁跳过钩子：除非用户明确要求并了解风险，否则不得使用 --no-verify。

避免重写已发布的历史：除非用户明确要求并确认了分支/远程上下文，否则不要进行强制推送（force-push）或在公共分支上变基。

提交前必做安全自检：检查是否包含密钥或凭据。

## 重要(优先级最高)

提交信息中不要包含 "Co-Author: ..." 字段。

## 工作流

- 检查仓库状态：

git status -sb

- 审查更改：git diff 和 git diff --staged

- 确认目标分支：git branch --show-current 以及（必要时）git remote -v

- 分析此次修改适用于单次提交还是拆分成多次提交

- 选择暂存策略：

  - 暂存全部：git add -A

  - 按文件暂存：git add path/to/file

- 编写提交信息并执行提交：

  - 使用约定式提交（可选）或标准的祈使句摘要。

- 提交后验证：

  - git show --stat
    可选：git log -n 5 --oneline --decorate

## 提交信息质量标准
始终追求：

标题 (Subject)：使用祈使句，意图明确，中文建议在 50 个字符以内（视仓库习惯而定）。

约定式提交 (可选)
如果仓库/团队使用约定式提交 (Conventional Commits)，请遵循：

feat(scope): ...（新功能）

fix(scope): ...（修复）

refactor(scope): ...（重构）

docs(scope): ...（文档）

test(scope): ...（测试）

chore(scope): ...（日常事务）

示例（推荐）
fix: 拒绝过期令牌

feat: 支持引号短语

refactor: 规范化错误响应

示例（需改进）
update stuff（太模糊）

fix（缺乏上下文）

WIP（除非用户或特定工作流明确要求，否则应避免）


---
name: git-commit
description: Craft clean, safe, and consistent Git commits. Use when the user asks to "commit" or "提交", write a commit message, split changes into multiple commits, stage hunks interactively, amend the last commit, create fixup/squash commits, prepare commits for PR review, or improve commit history (e.g., interactive rebase/autosquash) while avoiding accidental secrets or noisy diffs.
---

# Git Commit

## Overview

Create high-quality commits with minimal risk: review changes, stage intentionally, write a clear message, and commit using the right Git options for the situation.

## Default Guardrails

- Prefer small, atomic commits that are easy to review and revert.
- Do not run tests/compiles unless the user explicitly asks.
- Never bypass hooks with `--no-verify` unless the user explicitly asks and understands the risk.
- Avoid rewriting published history (force-push, rebases on shared branches) unless the user explicitly asks and confirms branch/remote context.
- Always sanity-check for secrets/credentials before committing.

## Important

Don't have "Co-Author: ..." in message.

## Workflow Decision Tree

1. Identify what the user wants:

- Single commit for current work
- Split work into multiple commits
- Amend the last commit
- Fix up/squash into an earlier commit (requires rebase)

2. Inspect repo state:
   - `git status -sb`
   - Review changes: `git diff` and `git diff --staged`
   - Confirm target branch: `git branch --show-current` and (if needed) `git remote -v`
3. Choose staging strategy:
   - Stage all: `git add -A`
   - Stage per file: `git add path/to/file`
   - Stage hunks interactively: `git add -p`
4. Write commit message and commit:
   - Conventional commits (optional) or standard imperative summary
5. Post-commit verification:
   - `git show --stat`
   - Optional: `git log -n 5 --oneline --decorate`

## Quick Start: Create One Good Commit

1. Review what will be committed:
   - `git status -sb`
   - `git diff --staged` (if already staged)
   - Otherwise: `git diff`
2. Stage intentionally:
   - `git add -p` (recommended) or `git add -A`
3. Re-review staged diff:
   - `git diff --staged`
4. Commit:
   - `git commit` (use editor) or `git commit -m "message"`
5. Verify:
   - `git show --stat`

## Commit Message Quality Bar

Always aim for:

- **Subject**: imperative mood, clear intent, ideally ≤ 50 characters in Chinese (repo norms may vary)

### Conventional Commits (Optional)

If the repo/team uses Conventional Commits, use:

- `feat(scope): ...`
- `fix(scope): ...`
- `refactor(scope): ...`
- `docs(scope): ...`
- `test(scope): ...`
- `chore(scope): ...`

### Examples (Good)

- `fix: 拒绝过期令牌`
- `feat: 支持引号短语`
- `refactor: 规范化错误响应）`

### Examples (Needs Work)

- `update stuff` (too vague)
- `fix` (no context)
- `WIP` (avoid unless explicitly requested by the user/workflow)

## Common Workflows

### Split Changes Into Multiple Commits

1. Decide slices (by feature/bugfix/refactor) and list the files/hunks per slice.
2. Reset staging to start clean:
   - `git reset`
3. For each slice:
   - Stage hunks: `git add -p` (repeat until slice complete)
   - Verify: `git diff --staged`
   - Commit with a message that matches only that slice.
4. Confirm remaining work:
   - `git status -sb` and `git diff`

### Amend the Last Commit

Use only if the last commit is not yet pushed/shared (or the user explicitly confirms rewriting history is OK).

- Amend message only: `git commit --amend`
- Amend content: stage changes then `git commit --amend`
- Keep message: `git commit --amend --no-edit`

### Fixup/Squash Into an Earlier Commit (Autosquash)

1. Identify the target commit:
   - `git log --oneline --decorate -n 20`
2. Create a fixup commit:
   - Stage changes, then `git commit --fixup <sha>`
3. Autosquash via interactive rebase:
   - `git rebase -i --autosquash <base>`

If the user is unsure about `<base>`, pick the parent of the target commit or rebase onto the branch point (ask if uncertain).

## Safety Checklist (Before Committing)

- Look for secrets in diffs: tokens, private keys, `.env`, credentials, connection strings.
- Avoid committing generated artifacts unless repo expects them.
- Ensure the commit contains only intended files/hunks: `git diff --staged`.
- If renames/moves occurred, prefer `git status` and `git diff --staged --name-status` to confirm.

## Questions to Ask the User (If Unspecified)

- Should the commit message follow Conventional Commits?
- Is it OK to amend/rewrite history on this branch (has it been pushed/shared)?
- Should the commit be signed (`-S`) or include `--signoff`?
- Are there multiple logical changes that should be split into separate commits?

---
description: 根据当前已暂存的 git 改动生成 Conventional Commits 风格的提交信息。当用户要求写 commit message、准备提交时使用。
---

# commit-message

## 当前改动

!`git diff --staged HEAD`

## 指引

基于上面的改动，生成一条 Conventional Commits 格式的提交信息：

- 类型选一：`feat` / `fix` / `docs` / `refactor` / `test` / `chore` / `perf`
- 格式：`<type>(<可选 scope>): <简短描述>`
- 视需要追加一个空行和正文，说明「为什么」改，而非「改了什么」
- 若暂存区为空，提示用户先 `git add`

只输出最终提交信息本身，不要多余解释。

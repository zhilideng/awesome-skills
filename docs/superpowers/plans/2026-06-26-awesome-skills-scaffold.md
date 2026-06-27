# awesome-skills 仓库骨架 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 为 awesome-skills 仓库搭建完整的 Claude Code plugin marketplace 骨架（两个独立 plugin + 开源配套文件 + 示例 skill），使其可被加载、可被 clone 安装。

**Architecture:** monorepo marketplace 模式。仓库根 `.claude-plugin/marketplace.json` 作为分发入口，下挂两个完全独立的 plugin：`plugins/my-skills`（个人原创，MIT）与 `plugins/collected-skills`（第三方收集，各保留原许可）。两者物理隔离，不共享文件。

**Tech Stack:** Claude Code plugin / Agent Skills 标准、JSON manifest、Markdown。无运行时代码、无单元测试——验证以「文件结构 + JSON 合法性 + plugin 加载发现」为准。

**对应 spec:** `docs/superpowers/specs/2026-06-26-awesome-skills-repo-structure-design.md`

---

## File Structure

| 文件 | 职责 |
|---|---|
| `.gitignore` | 忽略 IDE/系统/Python 缓存文件 |
| `LICENSE` | 根 MIT 许可，仅覆盖个人原创 + 仓库基础设施 |
| `.claude-plugin/marketplace.json` | marketplace 清单，列出两个 plugin |
| `plugins/my-skills/.claude-plugin/plugin.json` | 个人 plugin manifest（MIT） |
| `plugins/my-skills/README.md` | 个人 plugin 说明 |
| `plugins/my-skills/skills/commit-message/SKILL.md` | 示例个人 skill |
| `plugins/collected-skills/.claude-plugin/plugin.json` | 收集 plugin manifest（不声明单一 license） |
| `plugins/collected-skills/README.md` | 收集 plugin 说明 + 第三方内容声明 |
| `plugins/collected-skills/skills/summarize-changes/SKILL.md` | 示例收集 skill |
| `plugins/collected-skills/skills/summarize-changes/ATTRIBUTION.md` | 示例版权标注 |
| `README.md` | 仓库总览 + 安装 + 结构 + 贡献流程 |
| `CONTRIBUTING.md` | 贡献规范（两类 skill 流程） |
| `docs/skill-authoring-guide.md` | SKILL.md 编写规范 |
| `docs/attribution-guide.md` | 第三方 skill 收录与版权标注规范 |

---

## Task 1: 目录骨架与 .gitignore

**Files:**
- Create: `.gitignore`（目录由后续任务创建文件时自动生成）

- [ ] **Step 1: 创建全部目录**

Run:
```bash
mkdir -p .claude-plugin \
         plugins/my-skills/.claude-plugin \
         plugins/my-skills/skills/commit-message \
         plugins/collected-skills/.claude-plugin \
         plugins/collected-skills/skills/summarize-changes \
         docs/superpowers/specs \
         docs/superpowers/plans
```
Expected: 无输出，退出码 0。

- [ ] **Step 2: 写 `.gitignore`**

```
# IDE
.idea/
.vscode/

# OS
.DS_Store

# Python
__pycache__/
*.py[cod]
*.egg-info/
.venv/
venv/
```

- [ ] **Step 3: 验证目录骨架**

Run: `find . -type d -not -path './.git*' -not -path './.idea*' | sort`
Expected: 输出包含 `.claude-plugin`、`plugins/my-skills/.claude-plugin`、`plugins/my-skills/skills/commit-message`、`plugins/collected-skills/.claude-plugin`、`plugins/collected-skills/skills/summarize-changes`、`docs/superpowers/specs`、`docs/superpowers/plans`。

- [ ] **Step 4: Commit**

```bash
git add .gitignore
git commit -m "chore: add .gitignore and directory skeleton"
```

---

## Task 2: 根 LICENSE（MIT）

**Files:**
- Create: `LICENSE`

- [ ] **Step 1: 写 `LICENSE`（MIT 全文）**

```
MIT License

Copyright (c) 2026 zhili.deng

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

> 说明：此 MIT 仅覆盖 `my-skills/` 个人原创内容与仓库文档/基础设施；`collected-skills/` 第三方内容按各自原许可，见各 `ATTRIBUTION.md`。

- [ ] **Step 2: Commit**

```bash
git add LICENSE
git commit -m "docs: add MIT license (covers personal skills and infra)"
```

---

## Task 3: marketplace.json 与两个 plugin.json

**Files:**
- Create: `.claude-plugin/marketplace.json`
- Create: `plugins/my-skills/.claude-plugin/plugin.json`
- Create: `plugins/collected-skills/.claude-plugin/plugin.json`

- [ ] **Step 1: 写 `.claude-plugin/marketplace.json`**

```json
{
  "$schema": "https://json.schemastore.org/claude-code-marketplace.json",
  "name": "awesome-skills",
  "owner": { "name": "zhili.deng" },
  "plugins": [
    {
      "name": "my-skills",
      "source": "./plugins/my-skills",
      "description": "个人日常/学习/项目实战总结的 AI skill"
    },
    {
      "name": "collected-skills",
      "source": "./plugins/collected-skills",
      "description": "开源收集的常用 AI skill（各保留原作者许可）"
    }
  ]
}
```

- [ ] **Step 2: 写 `plugins/my-skills/.claude-plugin/plugin.json`**

```json
{
  "name": "my-skills",
  "version": "0.1.0",
  "description": "个人总结的 AI agent skill 集合",
  "author": { "name": "zhili.deng" },
  "license": "MIT",
  "homepage": "https://github.com/zhili.deng/awesome-skills"
}
```

- [ ] **Step 3: 写 `plugins/collected-skills/.claude-plugin/plugin.json`**

```json
{
  "name": "collected-skills",
  "version": "0.1.0",
  "description": "开源收集的常用 AI skill 集合（各 skill 保留原作者许可，见各自 ATTRIBUTION.md）",
  "author": { "name": "zhili.deng" },
  "homepage": "https://github.com/zhili.deng/awesome-skills"
}
```

> 注意：此处**故意不写 `license` 字段**——多许可混合不能声明单一 license。

- [ ] **Step 4: 校验三个 JSON 合法**

Run:
```bash
python3 -m json.tool .claude-plugin/marketplace.json >/dev/null && \
python3 -m json.tool plugins/my-skills/.claude-plugin/plugin.json >/dev/null && \
python3 -m json.tool plugins/collected-skills/.claude-plugin/plugin.json >/dev/null && \
echo "ALL JSON VALID"
```
Expected: 输出 `ALL JSON VALID`，退出码 0。若报错，检查逗号/引号后重跑。

- [ ] **Step 5: Commit**

```bash
git add .claude-plugin/marketplace.json plugins/my-skills/.claude-plugin/plugin.json plugins/collected-skills/.claude-plugin/plugin.json
git commit -m "feat: add marketplace.json and two plugin manifests"
```

---

## Task 4: my-skills 示例 skill 与说明

**Files:**
- Create: `plugins/my-skills/skills/commit-message/SKILL.md`
- Create: `plugins/my-skills/README.md`

- [ ] **Step 1: 写示例 skill `commit-message/SKILL.md`**

```markdown
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
```

- [ ] **Step 2: 写 `plugins/my-skills/README.md`**

```markdown
# my-skills

个人日常、学习、项目实战中总结的 AI agent skill 集合。

许可：MIT（见仓库根 `LICENSE`）。

## 包含的 skill

| Skill | 说明 |
|---|---|
| `commit-message` | 根据已暂存改动生成 Conventional Commits 提交信息 |

调用方式：`/my-skills:commit-message`
```

- [ ] **Step 3: 验证文件存在**

Run: `ls plugins/my-skills/skills/commit-message/SKILL.md plugins/my-skills/README.md`
Expected: 两行路径回显，无 "No such file"。

- [ ] **Step 4: Commit**

```bash
git add plugins/my-skills/skills/commit-message/SKILL.md plugins/my-skills/README.md
git commit -m "feat(my-skills): add commit-message example skill and readme"
```

---

## Task 5: collected-skills 示例 skill（含 ATTRIBUTION）与说明

**Files:**
- Create: `plugins/collected-skills/skills/summarize-changes/SKILL.md`
- Create: `plugins/collected-skills/skills/summarize-changes/ATTRIBUTION.md`
- Create: `plugins/collected-skills/README.md`

- [ ] **Step 1: 写示例收集 skill `summarize-changes/SKILL.md`**

（内容来自 Anthropic Claude Code 官方文档公开示例）

```markdown
---
description: Summarizes uncommitted changes and flags anything risky. Use when the user asks what changed, wants a commit message, or asks to review their diff.
---

## Current changes

!`git diff HEAD`

## Instructions

Summarize the changes above in two or three bullet points, then list any risks you notice such as missing error handling, hardcoded values, or tests that need updating. If the diff is empty, say there are no uncommitted changes.
```

- [ ] **Step 2: 写 `summarize-changes/ATTRIBUTION.md`（版权标注，必需）**

```markdown
# 版权与来源声明

- **原作者 / 来源仓库**：Anthropic（Claude Code 官方文档示例）
- **原链接**：https://code.claude.com/docs/en/skills
- **原许可证**：Anthropic 官方文档公开示例内容（按 Anthropic 文档使用条款）
- **收录日期**：2026-06-26
- **是否修改**：否

> 此 skill 为演示 collected-skills 结构所收录的公开示例。正式收录第三方 skill 时，请替换为本字段的真实信息；若原作者的许可证要求附带 LICENSE 文件，请将原 LICENSE 放入本 skill 目录。
```

- [ ] **Step 3: 写 `plugins/collected-skills/README.md`**

```markdown
# collected-skills

开源社区常用 AI agent skill 的收集（第三方内容）。

> ⚠️ 本目录为第三方收集内容，各 skill 版权归原作者所有，按各自原许可使用。本 plugin 不声明单一许可证；每个 skill 的来源与许可见其目录下的 `ATTRIBUTION.md`。

## 包含的 skill

| Skill | 来源 | 许可 |
|---|---|---|
| `summarize-changes` | Anthropic 官方文档示例 | 见 `ATTRIBUTION.md` |

调用方式：`/collected-skills:summarize-changes`

## 收录规范

收录新 skill 必须满足（详见 `../../docs/attribution-guide.md`）：

1. 取得原作许可（许可证允许再分发，或原作为公有领域/宽松许可）
2. 每个 skill 目录附 `ATTRIBUTION.md`，注明原作者、原链接、原许可证、收录日期、是否修改
3. 原许可证要求附 LICENSE 文件的，将原 LICENSE 放入该 skill 目录
```

- [ ] **Step 4: 验证文件存在**

Run: `ls plugins/collected-skills/skills/summarize-changes/SKILL.md plugins/collected-skills/skills/summarize-changes/ATTRIBUTION.md plugins/collected-skills/README.md`
Expected: 三行路径回显，无缺失。

- [ ] **Step 5: Commit**

```bash
git add plugins/collected-skills/skills/summarize-changes/SKILL.md plugins/collected-skills/skills/summarize-changes/ATTRIBUTION.md plugins/collected-skills/README.md
git commit -m "feat(collected-skills): add summarize-changes example skill with attribution"
```

---

## Task 6: 根 README 与 CONTRIBUTING

**Files:**
- Create: `README.md`
- Create: `CONTRIBUTING.md`

- [ ] **Step 1: 写根 `README.md`**

````markdown
# awesome-skills

个人日常、学习、项目实战中总结的 AI agent skill，以及开源常用 skill 的收集。

这是一个 [Claude Code](https://code.claude.com/) plugin **marketplace**，包含两个独立 plugin：

| Plugin | 内容 | 许可 |
|---|---|---|
| `my-skills` | 个人原创 skill | MIT |
| `collected-skills` | 开源收集的第三方 skill（各保留原许可） | 见各 skill 的 `ATTRIBUTION.md` |

## 安装

```bash
# 1. 添加本仓库为 marketplace
/plugin marketplace add zhili.deng/awesome-skills

# 2. 安装需要的 plugin
/plugin install my-skills@awesome-skills
/plugin install collected-skills@awesome-skills
```

安装后用 `/my-skills:<skill>` 或 `/collected-skills:<skill>` 调用。

## 目录结构

```
awesome-skills/
├── .claude-plugin/marketplace.json   # marketplace 清单
├── plugins/
│   ├── my-skills/                    # 个人原创 plugin（MIT）
│   └── collected-skills/             # 第三方收集 plugin（各按原许可）
├── docs/                             # 编写与收录规范
├── LICENSE                           # MIT（仅覆盖个人原创 + 基础设施）
└── CONTRIBUTING.md
```

## 贡献

- 新增个人 skill：见 `CONTRIBUTING.md` 的「新增个人 skill」
- 收录开源 skill：见 `CONTRIBUTING.md` 的「收录开源 skill」（必须附 `ATTRIBUTION.md`）

## 许可

仓库根 `LICENSE`（MIT）仅覆盖 `plugins/my-skills/` 个人原创内容与仓库文档/基础设施。`plugins/collected-skills/` 内为第三方内容，按各自原许可，详见各 `ATTRIBUTION.md`。
````

- [ ] **Step 2: 写 `CONTRIBUTING.md`**

````markdown
# 贡献指南

感谢为 awesome-skills 贡献 skill！本仓库分两类贡献流程。

## 一、新增个人 skill（放入 `plugins/my-skills/`）

1. 在 `plugins/my-skills/skills/` 下新建目录，目录名即 skill 名（kebab-case）：
   ```
   plugins/my-skills/skills/<your-skill>/SKILL.md
   ```
2. 编写 `SKILL.md`，规范见 [`docs/skill-authoring-guide.md`](docs/skill-authoring-guide.md)。核心：frontmatter 的 `description` 必填且写清「何时用」。
3. 个人 skill 默认采用仓库 MIT 许可，无需额外声明。
4. 在 `plugins/my-skills/README.md` 的 skill 表格中登记一行。

## 二、收录开源 skill（放入 `plugins/collected-skills/`）

1. **先确认许可**：原 skill 的许可证必须允许再分发（MIT/Apache-2.0/BSD/公有领域等）。
2. 在 `plugins/collected-skills/skills/` 下新建目录，建议沿用原名。
3. 放入 `SKILL.md`，并在**同目录**附 `ATTRIBUTION.md`，填写：
   - 原作者 / 来源仓库
   - 原链接
   - 原许可证
   - 收录日期
   - 是否修改（及修改说明）
4. 若原许可证要求保留 LICENSE 文件，将原 LICENSE 一并放入该 skill 目录。
5. 详细规范见 [`docs/attribution-guide.md`](docs/attribution-guide.md)。
6. 在 `plugins/collected-skills/README.md` 的 skill 表格中登记一行。

## 收录 checklist（收录开源 skill 前逐项确认）

- [ ] 已确认原许可证允许再分发
- [ ] 已附 `ATTRIBUTION.md` 且五项信息齐全
- [ ] 若需要，已附原 LICENSE 文件
- [ ] 已在对应 plugin 的 README 登记
- [ ] 本地已能通过 `/collected-skills:<skill>` 调用
````

- [ ] **Step 3: Commit**

```bash
git add README.md CONTRIBUTING.md
git commit -m "docs: add root README and CONTRIBUTING guide"
```

---

## Task 7: docs 规范文档

**Files:**
- Create: `docs/skill-authoring-guide.md`
- Create: `docs/attribution-guide.md`

- [ ] **Step 1: 写 `docs/skill-authoring-guide.md`**

````markdown
# Skill 编写规范

每个 skill 是一个目录，内含 `SKILL.md`：

```
skills/<skill-name>/
├── SKILL.md          # 必需
├── reference.md      # 可选：附加参考资料
└── scripts/          # 可选：辅助脚本
```

## SKILL.md 结构

```markdown
---
description: <一句话：何时该用这个 skill。这是 AI 决定是否自动加载的唯一依据，务必写清触发场景。>
---

# <Skill 名称>

<正文：给 AI 的指令/步骤/约束>
```

## frontmatter 字段

| 字段 | 必需 | 说明 |
|---|---|---|
| `description` | 是 | 何时用这个 skill；写清场景与意图 |
| `name` | 否 | 默认取目录名，一般不用写 |
| `disable-model-invocation` | 否 | 设为 `true` 则禁止 AI 自动调用，只能手动 `/调用` |

## 动态上下文注入

用 `` !`命令` `` 可在 skill 加载时执行命令并把输出注入正文（AI 看到的是执行结果）：

```
## 当前改动

!`git diff HEAD`
```

## 编写要点

- **description 决定一切**：写得像「在……情况下使用」，AI 才能在对的时机加载。
- **正文写给 AI**：用祈使句、步骤化；长参考材料放进 `reference.md`，按需加载。
- **单一职责**：一个 skill 解决一类问题；想做多件事就拆成多个 skill。
- skill 名用 kebab-case，与目录名一致。
````

- [ ] **Step 2: 写 `docs/attribution-guide.md`**

````markdown
# 第三方 skill 收录与版权标注规范

`plugins/collected-skills/` 收录第三方开源 skill 时，必须遵守本规范，确保版权与许可合规。

## 一、收录前：许可检查

只收录许可证**允许再分发**的 skill，例如：

- MIT、Apache-2.0、BSD-2/3-Clause、ISC（宽松许可，通常可收录）
- CC0 / 公有领域
- GPL/AGPL（可收录，但会传染下游——务必在 `ATTRIBUTION.md` 标注，提醒使用者）

来源不明的 skill **不要收录**。

## 二、每个 skill 必须附 `ATTRIBUTION.md`

放在该 skill 目录下，与 `SKILL.md` 同级：

```markdown
# 版权与来源声明

- **原作者 / 来源仓库**：<author / repo>
- **原链接**：<url>
- **原许可证**：<SPDX 标识或链接，如 MIT / Apache-2.0>
- **收录日期**：<YYYY-MM-DD>
- **是否修改**：<否 / 是，并说明改动>
```

## 三、保留原 LICENSE（如需）

若原许可证条款要求保留版权声明与 LICENSE 文件（MIT、Apache-2.0、BSD 等都有此要求），将该 skill 的原 LICENSE 文件复制进其目录：

```
skills/<collected-skill>/
├── SKILL.md
├── ATTRIBUTION.md
└── LICENSE          # 原 LICENSE（若原许可要求）
```

## 四、修改原 skill

若对收录的 skill 做了修改：

1. 在 `ATTRIBUTION.md` 的「是否修改」项说明改了什么
2. 部分 license（如 Apache-2.0）要求标注「已修改」——在 SKILL.md 顶部或 ATTRIBUTION 里注明
3. 保留原作者的版权声明

## 五、登记

在 `plugins/collected-skills/README.md` 的 skill 表格登记一行（名称 / 来源 / 许可）。
````

- [ ] **Step 3: Commit**

```bash
git add docs/skill-authoring-guide.md docs/attribution-guide.md
git commit -m "docs: add skill authoring and attribution guides"
```

---

## Task 8: 本地加载与发现验证（最终验证）

> 这是脚手架的「验收测试」：确认两个 plugin 都能被 Claude Code 加载、示例 skill 都能被发现。

**Files:** 无（纯验证）

- [ ] **Step 1: 校验全部 JSON 合法**

Run:
```bash
for f in .claude-plugin/marketplace.json \
         plugins/my-skills/.claude-plugin/plugin.json \
         plugins/collected-skills/.claude-plugin/plugin.json; do
  python3 -m json.tool "$f" >/dev/null || { echo "INVALID: $f"; exit 1; }
done
echo "ALL JSON VALID"
```
Expected: `ALL JSON VALID`。

- [ ] **Step 2: 校验目录约定（每个 skill 目录下有 SKILL.md；每个 collected skill 有 ATTRIBUTION.md）**

Run:
```bash
test -f plugins/my-skills/skills/commit-message/SKILL.md && echo "my ok" || echo "my MISSING"
test -f plugins/collected-skills/skills/summarize-changes/SKILL.md && echo "collected skill ok" || echo "collected skill MISSING"
test -f plugins/collected-skills/skills/summarize-changes/ATTRIBUTION.md && echo "attribution ok" || echo "attribution MISSING"
```
Expected: 三行均为 `... ok`。

- [ ] **Step 3: 本地加载 my-skills plugin（交互式，手动确认）**

Run（在项目根）:
```bash
claude --plugin-dir ./plugins/my-skills
```
Expected: Claude Code 启动。在会话中输入 `/my-skills:commit-message`，应能触发该 skill。
退出会话后继续。

- [ ] **Step 4: 本地加载 collected-skills plugin（交互式，手动确认）**

Run:
```bash
claude --plugin-dir ./plugins/collected-skills
```
Expected: 启动后 `/collected-skills:summarize-changes` 可调用。

- [ ] **Step 5: 最终提交（如有遗留改动）**

```bash
git status --short
# 若有未提交改动：
git add -A && git commit -m "chore: finalize scaffold"
# 若无输出（工作区干净），则跳过
```

---

## 完成标准

- [ ] 8 个 Task 全部完成
- [ ] 三个 JSON 合法
- [ ] 两个示例 skill 都能通过 `--plugin-dir` 加载并调用
- [ ] 每个 collected skill 都有 `ATTRIBUTION.md`
- [ ] 仓库可被 `/plugin marketplace add` 添加（推送到 GitHub 后验证）

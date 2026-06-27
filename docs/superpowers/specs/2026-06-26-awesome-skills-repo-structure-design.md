# awesome-skills 仓库结构设计

- **日期**：2026-06-26
- **状态**：已批准（待最终 review）
- **作者**：zhili.deng

---

## 1. 背景与目标

`awesome-skills` 是一个用于存放 **AI agent skill** 的开源仓库，收集两类内容：

- 个人日常、学习、项目实战中总结的**自创 skill**
- 开源社区常用的 skill（**第三方收集**）

目标是做成一个可被 Claude Code（及兼容 Agent Skills 开放标准的 AI 工具）直接加载使用的 plugin 仓库，同时作为开源项目供他人 clone 后作为 plugin 安装使用。

---

## 2. 核心需求（已与用户确认）

1. **内容类型**：AI Agent Skill —— Claude Code 风格的 frontmatter markdown，给 AI 加载调用。
2. **用途与受众**：开源分享，别人 clone 即可作为 plugin 安装使用。
3. **来源与版权**：自创与开源收集**分目录**，收集项**完整版权标注**。
4. **分类粒度**：暂只分「个人」与「开源收集」两大类，不再细分用途/技术领域（YAGNI；未来可扩展）。

---

## 3. 关键技术事实（官方文档查证）

这些事实决定了结构，不可凭记忆臆造：

- Claude Code plugin 的 `skills/` 目录是**扁平**的：一个直接子目录 = 一个 skill（`skills/<skill>/SKILL.md`）。**不支持分组子目录**；嵌套目录自动发现有未解决 bug（GitHub issue [#40640](https://github.com/anthropics/claude-code/issues/40640)、[#39787](https://github.com/anthropics/claude-code/issues/39787)）。
- plugin 结构：`.claude-plugin/plugin.json`（manifest）+ `skills/<skill>/SKILL.md`。
- plugin skill 是 **namespaced**：`/<plugin-name>:<skill-name>`。
- plugin 被复制到缓存，**不能引用自身目录外的文件**（如 `../shared-utils`）。→ 两个 plugin 必须完全独立。
- `marketplace.json` 放在仓库根 `.claude-plugin/marketplace.json`。字段：`$schema`、`name`、`owner{name,email}`、`plugins[]{name,source,description,version,author}`。`source` 可为本地路径 `./plugins/xxx` 或 git repo。
- SKILL.md frontmatter 核心字段：`description`（**必需**，决定 AI 何时加载）、`name`（可选，默认目录名）、`disable-model-invocation`（可选）。支持动态上下文注入 `` !`cmd` ``。
- marketplace 安装：`/plugin marketplace add <repo>` → `/plugin install <name>@<marketplace>`。

**来源**：[plugin-marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) · [skills](https://code.claude.com/docs/en/skills) · [plugins-reference](https://code.claude.com/docs/en/plugins-reference) · [create plugins](https://code.claude.com/docs/en/plugins)

---

## 4. 方案选择：B（monorepo + marketplace + 两个独立 plugin）

| 方案 | 结论 | 理由 |
|---|---|---|
| A. 单 plugin + 命名前缀 | 弃用 | 来源隔离弱、许可证只能统一 |
| C. 单 plugin + collected 纯参考 | 弃用 | 收集的 skill 不能直接加载，违背初衷 |
| **B. marketplace + 两个 plugin** | **采用** | 物理隔离、许可可不同、一条命令分发、可扩展、开源版权合规最佳 |

---

## 5. 详细设计

### 5.1 整体目录树

```
awesome-skills/
├── .claude-plugin/
│   └── marketplace.json              # marketplace 清单（仓库根，分发入口）
├── plugins/
│   ├── my-skills/                    # ① 个人自创 plugin
│   │   ├── .claude-plugin/plugin.json
│   │   ├── skills/
│   │   │   └── <skill-name>/SKILL.md
│   │   └── README.md
│   └── collected-skills/             # ② 开源收集 plugin（第三方）
│       ├── .claude-plugin/plugin.json
│       ├── skills/
│       │   └── <original-name>/
│       │       ├── SKILL.md
│       │       └── ATTRIBUTION.md    # ← 版权标注（每个 collected skill 必需）
│       └── README.md
├── docs/
│   ├── superpowers/specs/            # 设计文档（本文件所在）
│   ├── skill-authoring-guide.md      # 如何编写一个新 skill
│   └── attribution-guide.md          # 收集第三方 skill 的版权标注规范
├── README.md                          # 仓库总览 + 安装说明
├── LICENSE                            # MIT（仅覆盖个人原创部分）
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md                 # 开源标配（可选）
└── .gitignore
```

### 5.2 `.claude-plugin/marketplace.json`（仓库根）

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

> 安装方式：`/plugin marketplace add zhili.deng/awesome-skills` → `/plugin install my-skills@awesome-skills`

### 5.3 plugin.json

**`plugins/my-skills/.claude-plugin/plugin.json`**（个人）：
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

**`plugins/collected-skills/.claude-plugin/plugin.json`**（收集）：结构同上，但**不填 `license`** 字段——因为是多许可混合，单一 license 会构成虚假声明；各 skill 许可在 `ATTRIBUTION.md` 逐个声明。

### 5.4 SKILL.md 模板（两个 plugin 通用）

```markdown
---
description: <一句话：何时该用这个 skill——AI 决定是否加载的唯一依据>
---

# <Skill 名称>

<使用说明 / instructions / 可选的动态上下文注入 !`cmd`>
```

### 5.5 ATTRIBUTION.md 模板（每个 collected skill 必需）

```markdown
# 版权与来源声明
- 原作者 / 来源仓库：<author/repo>
- 原链接：<url>
- 原许可证：<SPDX 标识或链接，如 MIT / Apache-2.0>
- 收录日期：<YYYY-MM-DD>
- 是否修改：<否 / 是，并说明改动>
```

### 5.6 版权与许可策略（开源合规核心）

| 范围 | 许可 |
|---|---|
| 根 `LICENSE`（MIT） | 仅覆盖 `my-skills/` 个人原创 + 仓库文档/基础设施 |
| `collected-skills/` | **整体不适用单一许可**；每个 skill 保留原作者许可，记在各自 `ATTRIBUTION.md`；原作要求附 LICENSE 文件的，把原 LICENSE 放进该 skill 目录 |
| `collected-skills/README.md` | 顶部声明「本目录为第三方收集内容，版权归原作者，按各自许可」 |

### 5.7 开源配套文件

- **README.md**：项目定位 / 一条命令安装 / 目录结构说明 / 两条贡献流程（加个人 skill、收集开源 skill）
- **CONTRIBUTING.md**：个人 skill 编写规范 + 收集 skill 的版权标注 checklist
- **.gitignore**：`.idea/`、`.DS_Store`、`__pycache__/`、`*.pyc`
- **CODE_OF_CONDUCT.md**：可选

---

## 6. 未来扩展（YAGNI，暂不实现，仅记录正确做法）

- **如何细分**：当 skill 数量增多想按用途/技术领域细分时，**正确方式是拆成更多独立 plugin**（如 `my-python-skills`、`my-frontend-skills`）加入 `marketplace.json`，而**不是**在单个 plugin 的 `skills/` 下建子目录——后者会破坏 skill 自动发现。
- **CI 校验**：可加 GitHub Actions 校验 frontmatter 合法性、`collected-skills` 下每个 skill 必须有 `ATTRIBUTION.md`。

---

## 7. 落地步骤概览

1. 创建目录骨架与两个 `.claude-plugin/` manifest
2. 创建 `marketplace.json`
3. 写 README / LICENSE / CONTRIBUTING / .gitignore
4. 各放一个示例 skill（my-skills 一个、collected-skills 一个带 ATTRIBUTION）验证可加载
5. 用 `claude --plugin-dir ./plugins/my-skills` 本地测试

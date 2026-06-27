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

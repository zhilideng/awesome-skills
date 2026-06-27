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

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

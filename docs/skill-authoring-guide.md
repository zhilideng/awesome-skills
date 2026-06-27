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

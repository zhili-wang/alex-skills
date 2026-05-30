# Alex Skills

个人 AI 编码技能库，融合 Karpathy 行为准则与 Spec-Driven 需求澄清方法论。

## 技能列表

| 技能 | 说明 |
|------|------|
| [clarify](./skills/clarify/SKILL.md) | 需求澄清 — 结构化模糊性扫描 + 逐一提问，在写方案前消除需求歧义（适用于前端/后端/全栈） |

## 安装

**Claude Code 插件（推荐）**：

```bash
# 安装插件
/plugin marketplace add zhili-wang/alex-skills
/plugin install alex-skills@alex-skills
```

**手动安装**：将 `CLAUDE.md` 内容合并到项目的 CLAUDE.md 中。

## 行为准则

全局行为准则（CLAUSE.md）受 [Andrej Karpathy 的观察](https://x.com/karpathy/status/2015883857489522876) 启发：

1. **不假设，呈现权衡** — 说出假设，列出全部解读，每个选项附带 tradeoff
2. **简洁克制** — 不过度追问、不推测需求、一个问题一个决策点
3. **精准修改** — 只改相关章节、只追加不重写、清晰内容不动
4. **目标驱动** — 每个问题绑定一个决策、可验证的成功标准

## 许可

MIT
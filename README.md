# Context Engineer

AI 行为控制制品（Skill / Rule / Doc / Hook）的工程化设计工具。把提示词编写从即兴发挥变成可复现的工程流程——每个制品面向的读者是一个没有对话记忆的未来模型实例。

[English](README.en.md)

---

## 特性

- **情境驱动** — 先挖掘真实问题再选择制品类型，不是拿到需求就开始写
- **约束分级** — 高风险规则只给后果（防止模型绕过）；中风险带理由（支持推广）；偏好用轻约束
- **注意力预算** — Rule < 50 行，Skill 核心路径 < 500 行；冗余是缺陷不是完备
- **独立审查** — 写完后自动启动干净的 Agent 实例审查，作者的注意力池已被草稿污染
- **过期脚手架清除** — 为旧模型写的指令在新模型上是死重量，定期审计移除

完整的读者心理模型（7 个特征）和编写规则（R1–R8）在 [`skills/context-engineer/SKILL.md`](skills/context-engineer/SKILL.md)。

### 入口

| 命令 | 功能 | 触发方式 |
|------|------|---------|
| `/context-engineer` | 制品设计流程：挖掘情境 → 选择类型 → 失败模式纪律编写 → 独立审查 | "写一个 skill"、"我想让 Agent 在 X 时做 Y" |
| `/context-review` | 独立审查：干净 Agent 用结构化清单检查制品的结构性问题 | "审一下这个 skill"、"检查一下质量" |

两个入口共享同一套读者模型和审查清单。设计流程在第四步自动调用审查；审查也可独立使用。

---

## 示例

**`/context-engineer`：**

> 输入：*"我想让 Agent 做数据库迁移的时候更小心。"*
>
> Skill 先澄清：给谁用？上次出了什么事？有具体事故吗？判断应写成 Rule（行为约束）而非 Skill——因为行为始终生效。识别失败模式（跳过校验、未检查就报告成功），写出制品后启动独立审查。

**`/context-review`：**

> 输入：*"审一下 `skills/deploy-guard/SKILL.md`。"*
>
> 启动无上下文的独立 Agent，按严重程度报告：注意力预算超标、完备性幻觉、缺少理由的规则、过于刚性的边界判断。每项发现附具体改写。

---

## 模型校准

编写风格针对当代前沿模型调优：

- 正面指令优先于否定压制——现代模型默认克制，多数"不要做 X"是死重量
- 逐步精力标注——"这里仔细想" / "这步机械操作，别过度思考"
- 刚性规则只用于高风险场景，其余带理由支持推广

旧模型脚手架（"不要用 emoji"、"每 N 步总结"）已移除。

---

## 安装

```bash
git clone https://github.com/koukekoukej-glitch/context-engineer.git
cp -r context-engineer/skills/* <你的 skills 目录>/
```

或用符号链接：

```bash
ln -s "$(pwd)/context-engineer/skills/context-engineer" <skills 目录>/context-engineer
ln -s "$(pwd)/context-engineer/skills/context-review"   <skills 目录>/context-review
```

核心方法论可移植到任何 prompt 层——文件格式是约定，读者模型和编写纪律是通用的。

---

## License

[MIT](LICENSE)

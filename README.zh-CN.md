# Context Engineer

一个面向**上下文工程**的 Skill——为一个具体的读者（未来的模型实例）设计行为控制制品（Skill / Rule / Doc / Hook），不是反复修辞提示词。

**[English →](README.md)**

---

## 包含什么

一个 Skill，两个触发入口：

| 入口 | 做什么 | 什么时候调用 |
|------|--------|-------------|
| `/context-engineer` | 生产流程。挖掘真实处境，选对制品类型（Skill / Rule / Doc / Hook），按失败模式纪律写作，然后自动 spawn 独立审查员。 | "写一个 skill 做……"、"我希望 agent 在 Y 发生时做 X"、"改进这个提示词" |
| `/context-review` | 独立审查。Spawn 一个注意力干净的独立 Agent 审查已有制品，找会让模型静默失效的结构性问题，不做美观度检查。 | "审一下这个 skill"、"检查一下我这条 rule"、"review this skill" |

两个入口共享同一份读者心理模型和审查清单——生产流程在 Step 4 自动调用审查员；审查入口也可以独立使用在你没参与写的制品上。

---

## 为什么需要它

随手写提示词产出的制品，往往 demo 阶段工作、真实场景就漂移。这个 Skill 把每一个制品都当作**基础设施**——面向一个具体读者（对你的对话一无所知的未来模型实例），并强制执行：

- **先理解处境，再给方案。** "我想写一个代码审查 skill"可能意味着十种完全不同的东西，不问清就动手 = 必然返工。
- **约束按严重度分级。** 高危规则用**后果**而不是理由（防止模型自我合理化绕开）。中危规则必须带 **WHY**（激活举一反三）。偏好类轻触即可。
- **注意力预算是真预算。** Rule < 50 行，Skill 核心路径 < 500 行。冗余不是周到，是缺陷。
- **默认独立审查。** 作者刚写完时注意力池已被自己的草稿污染——新鲜的注意力池才能看见你看不见的。
- **清除过期 scaffolding。** 为老模型写的修正（"不要用 emoji"、"不要过度道歉"、"每 N 步总结"）在新模型上已无意义，仍占注意力预算，甚至和新默认冲突——每次模型升级后主动审视。

完整的读者心理模型（模型处理上下文的 7 条特性）和写作规则（R1–R8）都在 [`skills/context-engineer/SKILL.md`](skills/context-engineer/SKILL.md)。

---

## 典型会话

**使用 `/context-engineer`：**

> 用户：*"我希望 agent 更谨慎地执行数据库迁移。"*
>
> Skill 先追问：这个问题是你一个人的还是团队的？最近一次出事的具体情况？能举一个例子吗？根据回答判定：这是一个 **Rule**（50 行以内的常驻行为约束），而不是 Skill——因为行为是常驻的，不是多步工作流。命名可能的失败模式（跳过验证、没检查就声称完成），起草制品，然后 spawn 独立审查员，审查通过后才交付。

**使用 `/context-review`：**

> 用户：*"审一下 `skills/deploy-guard/SKILL.md`。"*
>
> Skill spawn 一个无对话上下文的独立 Agent，只传入制品和审查清单。按严重度汇报发现：注意力预算超限、完备性假象（模型会把列表当成完整清单）、缺少 WHY 的规则、该留判断空间却写死的规则。每条发现都带具体改写方案，不是泛泛的"建议改进"。

---

## 安装

这个 Skill 用的是通行的 skill 文件布局（带 YAML frontmatter 的 markdown + 可选 `references/` 目录）。如果你的 agent 宿主原生支持这种布局，直接把目录复制进它的 skills 目录即可：

```bash
git clone https://github.com/koukekoukej-glitch/context-engineer.git
cp -r context-engineer/skills/* <你的-skills-目录>/
```

或用 symlink，便于后续拉取更新：

```bash
ln -s "$(pwd)/context-engineer/skills/context-engineer" <你的-skills-目录>/context-engineer
ln -s "$(pwd)/context-engineer/skills/context-review"   <你的-skills-目录>/context-review
```

然后调用：

- `/context-engineer` — 设计新制品
- `/context-review` — 审查已有制品

两个入口也会在自然语言中触发（"写一个 skill 做……"、"审一下这个 rule"），不一定要打斜杠命令。

**宿主不识别这种布局？** 文件格式只是约定，但方法论——读者心理模型、约束分级、注意力预算、独立审查——是通用的。把 `SKILL.md` 的内容移植到你宿主对应的提示词层即可。

---

## 针对主流前沿模型校准

写作风格针对当前强指令遵循模型的默认行为校准：

- 优先用**正面引导**而非负面抑制——现代模型默认已收敛到节制，大多数"不要 X"型规则是无效负担。
- 支持**逐步 effort 标注**（"这一步要仔细推理"、"这是机械操作，别多想"）——近期模型新的可控维度，Skill 有意识利用。
- 刚性规则只用于真正的高危场景，大多数规则带 WHY，让模型能在未预见场景中泛化而不是死记硬背。

为老模型写的 scaffolding（"不要用 emoji"、"不要过度道歉"、"每 N 步总结进度"等）已被有意识地清除。

---

## License

[MIT](LICENSE)

---
name: baoyu-skill-craft
description: "Use when creating new skills, editing existing skills, or verifying skills — requires running baseline test before any content is written; treats skill creation as TDD for process documentation"
version: 2.0.0
---

# 小青蛙的 Skill 工艺手册 v2

> **核心原则：** 写技能就是写代码的 TDD。没有 baseline 失败测试 → 没有技能。

**REQUIRED BACKGROUND:** 你必须理解 `tdd` 技能的 RED-GREEN-REFACTOR 循环。本技能把它适配到文档工程。

## 什么是一个 Skill？

**Skill** 是可复用的技术/模式/参考指南，帮助未来的你找到并应用有效方法。

| 是 | 不是 |
|---|---|
| 可复用的技术、模式、工具、参考 | 一次性的奇技淫巧 |
| 跨项目通用的套路 | 项目专属约定（放 AGENTS.md/TOOLS.md） |
| 已验证的有效方法 | 你之前怎么解决某个问题的叙述 |

## Skill 三级分类

你的所有 skill 应显式标注类型：

### Technique（技术）
有明确步骤的具体方法。例子：`condition-based-waiting`, `root-cause-tracing`

### Pattern（模式）
解决问题的思维方式。例子：`flatten-with-flags`, `test-invariants`

### Reference（参考）
API 文档、语法指南、工具文档。例子：`feishu-channel-rules`, `baoyu-image-gen`

---

## TDD 映射

| TDD | Skill 创建 |
|---|---|
| **测试用例** | baseline 压力场景（subagent 运行） |
| **生产代码** | SKILL.md 文档 |
| **测试失败 (RED)** | 没有技能时 agent 违规 |
| **测试通过 (GREEN)** | 有技能时 agent 遵守 |
| **重构 (REFACTOR)** | 堵漏洞，保持合规 |
| **先写测试** | 写技能前先跑 baseline |
| **看到它失败** | 记录 agent 的实际违规行为 |
| **最小代码** | 只针对那些违规写指导 |
| **看到它通过** | 验证 agent 现在遵守了 |

## 铁律

```
没有 baseline 失败 → 不要写技能
```

NEW 技能和编辑已有技能都适用。先跑 baseline，记下 agent 怎么违规，再写。写之前就跑？**删掉重来。**

**无例外：**
- "很简单不需要" → 不，简单的事 agent 也会搞砸
- "我只是加一小段" → 不，加也要先验证
- "学术审查够了" → 阅读 ≠ 使用，跑场景
- 不能保留"作为参考"重新改写
- 不能"改一改用"跳过测试
- **删就是删**

## 形式匹配失败类型（v2 新增核心）

写指导之前，先分类 baseline 失败的类型**用错误的形式对一个失败类型做指导，不但不解决，还可能让问题更糟。**

| Baseline 失败类型 | 正确的形式 | 错误的形式 |
|---|---|---|
| **知道规则但违反**（压力下绕过） | 禁令 + rationalization table + red flags | 软指导（"建议..."、"考虑..."） |
| **输出形状不对**（叙述太长、没结论、重述需求而不是回答） | **正向 Recipe**：说清楚输出是什么 — 它的部件、顺序 | 禁止列表（"不要叙述"、"不要重述"） |
| **遗漏必需项**（应该填的字段没填） | **结构性强制**：在已有模板里加 REQUIRED 字段 | 在模板旁边用散文提醒 |
| **行为应取决于条件**（不同场景应该不同回应） | **条件谓语**：以可观察的谓词为 key（"如果 brief 存在，引用它"） | 无条件规则 + 豁免条款 |

### 为什么禁令反作用于 Shape 问题

实测禁语言（"不要做 X"）在 agent 有竞争目标时会让 agent 更倾向做 X。Shape 问题要用 Recipe — 说清楚输出应该是什么形状，agent 不需要"协商"。

```
# ❌ Shape 问题用禁令 → 反效果
"不要复述需求" → agent 更加复述

# ✅ Shape 问题用 Recipe
"输出的结构是：
1. [一句话判决结果]
2. [关键证据/理由]
3. [下一步建议]"
```

**形式决策微测试：** 如果不确定属于哪种失败，在真实上下文中对两种形式各跑 5+ 次单次调用，看哪个收敛。

---

## SKILL.md 结构

### Frontmatter（YAML）

```yaml
---
name: skill-name-with-hyphens
description: "Use when [具体的触发条件和症状] — 只写 WHEN TO USE，不写 HOW TO DO"
---
```

**命名规则：**
- 只用小写字母、数字、连字符（无括号、特殊字符）
- 动词先行主动语态：`writing-skills` 不是 `skill-creation`
- 用 -ing 动名词表示过程：`debugging-with-logs`

**Description 铁律：只写触发条件，不写工作流**

```yaml
# ❌ 坏：总结了工作流 — agent 可能直接跟描述走，不读全文
description: Use when executing plans - dispatches subagent per task with code review

# ✅ 好：只写触发条件
description: Use when executing implementation plans with independent tasks
```

**为什么？** 实测显示，当 description 总结了工作流，agent 可能会只按描述做而不读 SKILL.md 整体。描述的工作流越详细，agent 读 FULL CONTENT 的概率越低。

**改动 description 的规则：** 旧的 description 如果包含工作流总结，把它从 description 移到 SKILL.md 正文的 Overview 段。description 只保留触发条件。

### 正文结构

```markdown
# Skill 名称

## 概述
核心原则，1-2 句话

## 何时使用
- 症状和用例列表
- 何时不适合

## 核心模式
Before/After 对比

## 快速参考
表格或列表

## 实现细节
内嵌代码或链接

## 常见错误
什么会错 + 如何修

## 真实案例（可选）
具体效果
```

---

## 微测试（Micro-Test）流程（v2 新增）

在全量压力场景之前，先用微测试验证措辞本身。

**为什么需要：** 全量压力测试慢且贵，微测试可以在 1-2 分钟内验证 wording 是否 work。

### 步骤

1. **每次一个新鲜上下文样本** — 一个裸 API 调用或单次 subagent。System prompt = 技能/指令的真实上下文；User message = 容易触发该失败的任务
2. **总是包含一个无指导的对照组** — 如果对照组没有出现该失败，停止——本不需要这条指导
3. **每个变体 5+ 次** — 单次样本会说谎
4. **手动阅读每个匹配** — 模板回显和引用的反例会伪装成匹配
5. **方差是一个指标** — 5 次跑出 5 种不同解释 = 措辞不够严格

微测试验证措辞，不替代完整压力场景（discipline 技能仍需完整）。

---

## Skill 目录结构

```
skills/
  skill-name/
    SKILL.md              # 主文档（必需）
    supporting-file.ext   # 只有必要时
```

**扁平命名空间** — 所有 skill 在一个平面空间内搜索。

**分离规则：**
- 超过 100 行的重型参考 → 独立文件
- 可复用的工具脚本 → 独立文件
- 其他全部内嵌 SKILL.md

---

## Skill Discovery Optimization (SDO v2)

### 1. Description — 只写触发条件，禁止工作流

重复强调：Description 唯一功能是在 Agent 需要时触发加载。**绝对不能**在里面写步骤。

```yaml
# ❌ 坏：写了工作流
description: Use when creating skills - run baseline, write minimal, test, refactor, close loopholes

# ✅ 好：只写触发条件
description: Use when creating new skills — requires baseline test before any content is written
```

如果 baseline 测试发现 agent 只读 description 没读正文 → 把 description 里所有工作流内容删掉。

### 2. Keywords 关键词覆盖

用 agent 会搜的词：
- 错误消息："Hook timed out", "ENOTEMPTY", "race condition"
- 症状："flaky", "hanging", "zombie", "pollution"
- 同义词："timeout/hang/freeze", "cleanup/teardown/afterEach"
- 工具名：实际命令、库名、文件类型

### 3. Token 效率

经常加载的技能（如入口 skill）要紧凑：
- 入门类：<150 词
- 常加载：<200 词
- 其他：<500 词

**压缩技巧：**
- 详细参数 → 引用 `--help` 而不是罗列所有 flag
- 重复内容 → 引用其他 skill 而不是重复写
- 一个高质量的例子 > 多个平庸的例子

### 4. 交叉引用

用 `**REQUIRED SUB-SKILL:**` 和 `**REQUIRED BACKGROUND:**` 标记必需前置 skill：

```markdown
**REQUIRED BACKGROUND:** 你必须理解 `tdd` 的 RED-GREEN-REFACTOR 循环。
**REQUIRED SUB-SKILL:** 使用 `subagent-review-workflow` 执行审查。
```

**不要用路径：** `❌ skills/subagent-review-workflow/SKILL.md` → `✅ subagent-review-workflow`
**不要用 `@` 语法：** `@` 会立即强制加载文件，烧掉 200k+ 上下文。

---

## 防推理盾（Bulletproofing v2）

Agent 很聪明，会在压力下找漏洞。不只是列规则，还要预判怎么绕。

### 堵每个明确的漏洞

<Bad>
```markdown
写技能前不跑 baseline？删掉。
```
</Bad>

<Good>
```markdown
写技能前不跑 baseline？删掉。重新来。

**无例外：**
- 不能保留"作为参考"
- 不能"改一改用"
- 不能跳过测试
- 删就是删
```
</Good>

### 用 Rationalization Table

从 baseline 测试中收集 agent 的理山。每个借口都要在表里有对应：

```markdown
| 借口 | 现实 |
|------|------|
| "太简单了不需要" | 简单的东西 agent 也会搞砸。测试只需 30 秒。 |
| "我之后会测" | 先跑和后跑完全不同。后面跑 = "那代码做了什么？" |
| "没时间测试" | 发布未测试的 skill 花费更多时间修复。 |
```

### 用 Red Flags 自查

```markdown
## Red Flags — 停，重新开始

- 没跑 baseline 就写
- "我隐约记得这个 skill 的内容"
- "这个 skill 不会变"
- "我手动验证过了"
- "这是唯一的做法，不需要 skill"
```

## 关于 Spirit vs Letter

```markdown
**违反文字就是违反精神。**
```
这条堵住了 "我是按精神做的，不是按文字" 这类借口。加上之后就消除整类辩解。

---

## Flowchart 使用

只在以下场景使用 flowchart：
- 非显而易见的决策点
- 容易提前停下的循环流程
- "什么时候用 A vs B" 的判断

**永远不用 flowchart 的场景：**
- 参考材料 → 用表格
- 代码示例 → Markdown 代码块
- 线性步骤 → 数字列表

**Graphviz 风格：** 如果渲染 flowchart，用 `render-graphs.js` 或类似工具渲染成 SVG。不要用文字描述流程图。

---

## 示例代码

**一个好例子胜过多个平庸的例子。**

选择最相关的语言写，不要多语言稀释。一个完整的、带注释 WHY 的、来自真实场景的例子就够了。

不要写：
- 5+ 种语言的实现
- 填空式模板
- 编造的例子

---

## PR Contribution 指南（如果贡献到开源技能生态）

如果技能要 PR 到外部仓库（如 superpowers）：

- 先搜索已存在 PR（open + closed），不要开重复
- 完整填写 PR 模板，不空段
- 披露模型和 harness 信息
- 更改行为塑造内容的技能需要 eval 证据
- 无人类参与的 PR 会被关闭（如 superpowers 的 94% 拒绝率）

---

## 写完后的自检列表

**每个 skill 创建/修改都必须过一遍：**

- [ ] 跑过 baseline（没 skill 时 agent 的行为记录）
- [ ] name 只用字母数字连字符
- [ ] description 只写触发条件，不写工作流
- [ ] description 第三人称
- [ ] 有关键词覆盖（错误、症状、工具名）
- [ ] 明确的概述和核心原则
- [ ] 针对 baseline 发现的具体违规写指导
- [ ] **指导形式匹配失败类型**（禁令/配方/结构/条件谓语）
- [ ] 验证了有 skill 后 agent 遵守
- [ ] 堵了 agent 的新借口
- [ ] 有 rationalization table
- [ ] 有 red flags
- [ ] **有 Spirit vs Letter 堵漏洞声明**
- [ ] Token 效率合规（常加载 <200 词，其他 <500）
- [ ] 只用标准引用格式（`**REQUIRED:**`），不用 `@` 或路径

## 关键教训

来自 obra/superpowers v6 + compound-engineering 的实际经验：

1. **没有 baseline 失败 → 你不知道 skill 是否解决了对的问题**
2. **description 总结工作流 → agent 不读全文** → 只写触发条件
3. **禁令 → 适合 discipline 失败，配方 → 适合 shape 失败**
4. **结构强制 → 适合遗漏失败，条件谓语 → 适合条件失败**
5. **agent 找到新借口 → 就往 rationalization table 加**
6. **一个例子足够 → 多语言稀释降低质量**
7. **<150 词是入口 skill 的目标** — 每个 token 都贵
8. **每次都带无指导对照组 → 才知道指导是否真的在起作用**

## 相关技能

**REQUIRED BACKGROUND:** `tdd` — TDD 的 RED-GREEN-REFACTOR 循环
**RECOMMENDED:** `baoyu-output-quality` — 输出质量审核
**RECOMMENDED:** `baoyu-dev-lifecycle` — 开发生命周期流程

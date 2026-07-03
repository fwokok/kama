---
name: subagent-parallel
description: "Use when facing 2+ independent tasks that can be worked on without shared state or sequential dependencies — dispatches focused subagents per independent domain; each runs in parallel"
---

# 并行 SubAgent 任务分发

> 从 obra/superpowers v6.1.1 的 dispatching-parallel-agents + subagent-driven-development 提取。

## 核心原则

```
任务数 > 1 & 独立 & 无共享状态 → 并行分发
```

当多个无关任务可以同时进行时，逐个排查浪费时间。每个任务是独立的，应该同时进行。

**不要：** 开一个 subagent 干所有事 → 它需要理解所有领域的上下文
**应该：** 每个独立领域开一个 subagent → 各司其职，并行运行

---

## 何时使用

- 3+ 个测试文件出现不同根因的失败
- 多个子系统独立出问题
- 每个问题不需要其他问题的上下文就能理解
- **没有共享状态**（不编辑同一个文件、不使用同一资源）

## 何时不使用

- 问题是相关的（修一个可能顺带修另一个）
- 需要理解完整系统状态
- 尚在探索性调试（还不知道问题是什么）
- subagent 会互相干扰（编辑同一文件/资源）

---

## 工作流

### Step 1: 识别独立领域

把问题按领域分组。每个领域应该：
- 可以被独立理解（不需要另一个领域的输出）
- 修一个不影响另一个
- 涉及不同的子系统/文件

**示例：**
```
领域 A: 工具审批流程（agent-tool-abort.test.ts）
领域 B: 批量完成任务（batch-completion-behavior.test.ts）
领域 C: 中止逻辑（tool-approval-race-conditions.test.ts）
```

### Step 2: 创建聚焦的 Agent Task

每个 subagent 只接一个领域的任务。prompt 必须包含：

1. **明确的范围** — 一个测试文件或一个子系统
2. **明确的目标** — "让这些测试通过"
3. **约束条件** — "不要改其他代码"
4. **期望输出** — "返回你发现了什么 + 改了什么的摘要"

```
Subagent A (general-purpose): "Fix agent-tool-abort.test.ts failures"
Subagent B (general-purpose): "Fix batch-completion-behavior.test.ts failures"
Subagent C (general-purpose): "Fix tool-approval-race-conditions.test.ts failures"
```

**关键：** 所有 dispatch 调用在同一轮中发出。调用链中同时分发多个 = 并行。

### Step 3: 汇总和集成

当所有 subagent 返回后：
1. 读每个的摘要
2. 检查是否有冲突（是否改了同一文件）
3. 跑完整测试套件
4. 集成所有变更

---

## Agent Prompt 结构

好的 prompt 必须：
1. **聚焦** — 一个明确的问题领域
2. **自包含** — 理解问题需要的所有上下文都在里面
3. **明确输出** — 告诉 agent 应该返回什么

```markdown
修复 src/tests/agent-tool-abort.test.ts 的 3 个失败测试：

1. "should abort tool with partial output capture" — 期望消息中含 interrupted at
2. "should handle mixed completed and aborted tools" — fast tool 被中止而不是完成
3. "should properly track pendingToolCount" — 期望 3 结果但得到 0

这些是时序/竞态条件问题。你的任务：

1. 读测试文件，理解每个测试验证什么
2. 识别根因 — 时序问题还是实际 bug？
3. 修复方式：
   - 用事件驱动的等待替代任意 timeout
   - 如果发现了 abort 实现中的 bug，修复它
   - 如果测试预期和变更后的行为不一致，调整预期

不要只是延长 timeout — 找到真正的问题。

返回：你所发现和修复的摘要。
```

## 常见错误

| 错误 | 示例 | 修正 |
|------|------|------|
| 范围太宽 | "修复所有测试" | 指定具体文件 |
| 没给上下文 | "修复这个竞态" | 贴错误消息和测试名 |
| 没加约束 | 无 | "不要改生产代码" |
| 输出定义模糊 | "修一下" | "返回根因和变更摘要" |

## 示例：真实场景

**场景：** 大重构后 6 个测试失败，跨 3 个文件

```
领域 A: agent-tool-abort.test.ts — 3 个失败（时序问题）
领域 B: batch-completion-behavior.test.ts — 2 个失败（工具未执行）
领域 C: tool-approval-race-conditions.test.ts — 1 个失败（execution_count=0）
```

**判定：** 各自独立（中止逻辑 ≠ 批量完成 ≠ 竞态条件）

**分发：**
```
Agent 1 → Fix agent-tool-abort.test.ts
Agent 2 → Fix batch-completion-behavior.test.ts
Agent 3 → Fix tool-approval-race-conditions.test.ts
```

**结果：**
- Agent 1: 用事件驱动等替代 timeout
- Agent 2: 修复 event 结构 bug（threadId 放错位置）
- Agent 3: 加上 async tool execution 完成的等待

**集成：** 所有修复不冲突，全绿。3 个问题在 1 个问题的时间内解决。

## 相关技能

**REQUIRED SUB-SKILL:** 汇总后加载 `baoyu-output-quality` 验证各 subagent 的结果不冲突。
**RECOMMENDED:** `subagent-review-workflow` — 单任务 review 流程，本技能是其并行扩展。

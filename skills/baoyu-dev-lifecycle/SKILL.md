---
name: baoyu-dev-lifecycle
description: "Use when starting a multi-step development or creative task — sets the Phase workflow from design through delivery. Entrypoint for any non-trivial project."
version: 2.0.0
---

# 小青蛙开发生命周期 v2

> 从 google/agents-cli 的 Phase 生命周期 + obra/superpowers 的 Brainstorming→Writing Plans→Execution 三阶段 + EveryInc 的 6-step Compound Engineering 循环演化而来。

## 核心思想：先理解，再动手，然后复合

```
Phase 0 — 理解需求
Phase 1 — 设计方案
Phase 2 — 构建实现
Phase 3 — 评估验证
Phase 4 — 交付输出
      然后 ↺
Phase 5 — 知识复合（Compound）：把学到的东西固化，让下一轮更轻松
```

**每一个 Phase 有独立的退出标准。没达到不能进下一个。**

---

## PHASE 0 — 理解

**必须问的问题：**
1. 做什么？— 核心目的和范围
2. 为什么？— 给谁用、解决什么问题
3. 已知约束？— 技术栈、时间、安全、合规
4. 成功标准是什么？— 可衡量的结果

**反模式：太简单了不需要理解**
每个项目都走这个流程。一个函数、一个配置项、一篇文章——都一样。"简单"的项目正是遗落假设最多的地方。理解可以很短（简单项目两三句），但必须有。

### Exit：对要做什么有共识

---

## PHASE 1 — 设计

**REQUIRED:** 提出设计方案前，先用 `baoyu-output-quality` 检查你的设计是否清晰。

### 步骤
1. 探索当前上下文（文件、代码、已有设计）
2. 一次一个问题澄清细节
3. 提出 2-3 种方案，带优缺点对比
4. 给出你的推荐+理由
5. 逐段呈现设计，获得逐段批准
6. 写设计文档

### Exit：设计文档写好，你审批通过

---

## PHASE 2 — 构建

**核心策略：原型优先（Prototype First）**

先以最快速度做出最小可用版本 → 验证 → 再添加部署/CI/CD/规模化部分。

### 编写原则
- **一次改一个维度** — 同时改 instruction + tool + config → 不知道哪个修好了或搞砸了
- **代码保全** — 只改你指定的行，不碰周围的
- **频繁提交** — 每个独立步骤就 commit
- **保持隔离** — 新功能开新分支

### Exit：代码/内容写好，自测通过

---

## PHASE 3 — 评估

**REQUIRED SUB-SKILL:** 执行评估前加载 `baoyu-output-quality`，使用其 pre-flight checklist 做质量验证。

### Quality Flywheel
```
准备 → 运行 → 评分 → 分析 → 修复 → 重跑
```

每一轮迭代：
1. **运行**: 执行测试/评估命令
2. **评分**: 看结果（分数、失败数、退出码）
3. **分析**: 什么失败了？为什么？
4. **修复**: 一次修一个问题
5. **重跑**: 确认修复有效，不引入回归

### Exit：所有验证通过，质量分数 >= 7

---

## PHASE 4 — 交付

**REQUIRED SUB-SKILL:** 交付前必须加载 `baoyu-output-quality` 运行 pre-flight checklist。

### Exit：交付完成

---

## PHASE 5 — 知识复合（新！从 compound-engineering 吸收）

**核心思想：** 每次完成一个任务，你的团队应该比之前聪明一点点。

解决的问题还没被固化，下一次遇到还要重新研究。**知识不复合 = 每次从零开始。**

### 什么时候做

每次 Phase 3-4 完成后，如果满足以下任意一条：
- 解决了一个有难度的问题
- 发现了一个之前不知道的配置/API 细节
- 纠正了一个常见错误
- 学到了一种模式或技巧

### 做什么

**记录到 `docs/solutions/` 目录**（如果项目没有，先创建）：

```
docs/solutions/
  YYYY-MM-DD-简短描述.md
```

**记录格式：**
```markdown
---
title: 简短描述
created: YYYY-MM-DD
category: [debug | config | pattern | api | tool]
related_skills: [如果适用]
---

## 问题
一句话描述

## 根因
什么导致了这个问题

## 解决方案
关键步骤，突出 WHY 而不是每个按键

## 下次怎么做
- 检查点/预防措施
- 从 Phase 0/1 就可以加入的问题

## 类似场景
其他可能出现此问题的地方
```

**原则：**
- 记录 **模式** 而不是 **按键序列**（"用 `xargs` 处理" 不是 "输入 `| xargs -I {} echo {}`"）
- 保持记录 < 20 行正文。太长 = 没人读
- 如果有对应 skill，直接在 skill 里引用本次记录

### 知识复合的飞轮效应

```
没有复合: 解决一个问题 → 2 小时后忘记 → 下一次重新研究 1 小时
有复合:   解决一个问题 → 2 分钟记录 → 下一次 5 分钟搞定
```

每次复合，下一次的 Phase 0-1 会更短 = 飞轮加速。

---

## 整生命周期对照表

| Phase | 做什么 | 产出 | 引用的 skill |
|-------|--------|------|-------------|
| 0 — 理解 | 问问题 | 共识列表 | — |
| 1 — 设计 | 方案+规格 | 设计文档 | `baoyu-output-quality` |
| 2 — 构建 | 写代码/内容 | 可运行版本 | — |
| 3 — 评估 | 验证+修复 | 通过报告 | `baoyu-output-quality` |
| 4 — 交付 | 质量闸+发出 | 最终输出 | `baoyu-output-quality` |
| 5 — 复合 | 固化知识 | `docs/solutions/` 记录 | — |

## 快捷路径

**简单任务（5 分钟内可完成）：**
Phase 0（1问）→ Phase 2 → Phase 3 → Phase 4 → 可选 Phase 5

**复杂项目（多个独立子系统）：**
分解成子项目。每个子项目走完整生命周期。先做核心子系统。

**Bug 修复：**
Phase 0（确认症状）→ 直接修 → Phase 3（验证修复）→ Phase 4 → Phase 5

---

## 技能链式调用

Dev-lifecycle 与其他 skill 的链条关系：

```
baoyu-dev-lifecycle (入口)
  ├─ Phase 0-1 → baoyu-output-quality (检查设计方案)
  ├─ Phase 3   → baoyu-output-quality (质量验证 + pre-flight)
  │                └→ baoyu-skill-craft (如果发现新技能需求)
  ├─ Phase 4   → baoyu-output-quality (交付前检查)
  └─ Phase 5   → (可能发现新问题 → 回到 Phase 0)
```

**链式调用的原则：**
- 入口 skill（这个）是"主路由" — 决定当前在哪个 Phase
- 子 skill 不可反向调用主路由（baoyu-output-quality 不叫 baoyu-dev-lifecycle）
- 子 skill 可以互相调用（baoyu-output-quality 可以推荐 baoyu-skill-craft）
- 每个 skill 只关心自己的 layer

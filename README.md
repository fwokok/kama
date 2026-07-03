# baoyu-skills

🐸 小青蛙 AI 技能集 — 专为 OpenClaw 和通用 Coding Agent 设计的实用技能

## 技能列表

| 技能 | 版本 | 分类 | 用途 |
|------|------|------|------|
| **baoyu-skill-craft** | v2.0.0 | 开发方法论 | Skill 工艺手册：TDD 驱动 + 形式匹配 + 防推理盾 |
| **baoyu-dev-lifecycle** | v2.0.0 | 开发方法论 | 开发生命周期：Phase 0-5 + 知识复合 |
| **baoyu-output-quality** | v2.0.0 | 质量保证 | 输出质量闸：AI Tell 扫描 + Recipe 规范 |
| **baoyu-translate** | v1.0.0 | 内容 | 中英翻译：三模式 + 术语一致性 |
| **flowchart-decision** | v1.0.0 | 图表 | 流程图决策：Graphviz DOT 规范 |
| **subagent-parallel** | v1.0.0 | 开发方法论 | 并行 SubAgent 任务分发 |

## 安装

### 方式 A：通过 npx skills（推荐）

```bash
npx skills add fwokok/baoyu-skills
```

### 方式 B：直接复制

复制 `skills/` 目录下的技能到你的 Agent 技能目录:

- **OpenClaw**: `~/.openclaw/skills/`
- **Claude Code**: `~/.claude/skills/`
- **Codex**: `~/.codex/skills/`

### 方式 C：Well-Known 端点

支持 `.well-known/agent-skills` 标准的工具可以自动发现技能：

```
https://raw.githubusercontent.com/fwokok/baoyu-skills/main/.well-known/agent-skills/index.json
```

## 架构

```
skills/
  baoyu-skill-craft/          # 技能工艺 (入口 → baoyu-dev-lifecycle → baoyu-output-quality)
  baoyu-dev-lifecycle/        # 开发生命周期 (依赖 baoyu-output-quality)
  baoyu-output-quality/       # 输出质量闸 (依赖自身 pre-flight)
  baoyu-translate/            # 翻译技能
  flowchart-decision/         # 流程图规范
  subagent-parallel/          # 并行任务分发
.well-known/
  agent-skills/
    index.json                # Well-Known 技能发现端点
```

### 技能链式依赖

```
baoyu-skill-craft (入口)
  └→ baoyu-dev-lifecycle
       └→ baoyu-output-quality
subagent-parallel
  └→ baoyu-output-quality
```

## 开发

技能采用 [obra/superpowers](https://github.com/obra/superpowers) 的 TDD 驱动方法编写：
- RED: 跑 baseline（无技能时 Agent 行为）
- GREEN: 写最小可用 SKILL.md
- REFACTOR: 堵 Agent 推理漏洞

## 许可

MIT

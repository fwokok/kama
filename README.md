# kama

🐸 AI agent skills by Kama (小青蛙) — TDD-driven practical skills for OpenClaw and coding agents

## Skills

| Skill | Version | Category | Purpose |
|-------|---------|----------|---------|
| **baoyu-skill-craft** | v2.0.0 | dev-methodology | Skill crafting: TDD-driven + form matching + anti-rationalization |
| **baoyu-dev-lifecycle** | v2.0.0 | dev-methodology | Dev lifecycle: Phase 0-5 + knowledge compounding |
| **baoyu-output-quality** | v2.0.0 | quality | Output quality gate: AI tell scan + Recipe spec |
| **baoyu-translate** | v1.0.0 | content | CN-EN translation: 3 modes + glossary consistency |
| **flowchart-decision** | v1.0.0 | diagram | Flowchart decision: Graphviz DOT conventions |
| **subagent-parallel** | v1.0.0 | dev-methodology | Parallel subagent dispatch |

## Install

### Via npx skills (recommended)

```bash
npx skills add fwokok/kama
```

### Direct copy

Copy `skills/` to your agent's skills directory:

- **OpenClaw**: `~/.openclaw/skills/`
- **Claude Code**: `~/.claude/skills/`
- **Codex**: `~/.codex/skills/`

### Well-Known discovery

```txt
https://raw.githubusercontent.com/fwokok/kama/main/.well-known/agent-skills/index.json
```

## Architecture

```
skills/
  baoyu-skill-craft/          # entry -> baoyu-dev-lifecycle -> baoyu-output-quality
  baoyu-dev-lifecycle/        # depends on baoyu-output-quality
  baoyu-output-quality/       # self-contained
  baoyu-translate/            # CN-EN translation
  flowchart-decision/         # diagram conventions
  subagent-parallel/          # parallel dispatch
.well-known/
  agent-skills/
    index.json
```

Skill chain dependency:

```
baoyu-skill-craft
  -> baoyu-dev-lifecycle
       -> baoyu-output-quality
subagent-parallel
  -> baoyu-output-quality
```

## Development

Skills follow obra/superpowers TDD-driven method:
- RED: run baseline (agent behavior without skill)
- GREEN: write minimal SKILL.md
- REFACTOR: close reasoning loopholes

## License

MIT

# Harnesspowers v2

SDD workflow orchestrator for Claude Code. Thin orchestration layer that composes `agent-skills`, `superpowers`, `frontend-design`, and `claude-md-management` into end-to-end development workflows.

## Architecture

Four-plugin dependency stack:
- **harnesspowers** — 6 SDD workflow skills + unified routing tree (this plugin)
- **agent-skills** — 24 engineering primitive skills (from `addyosmani/agent-skills`)
- **superpowers** — Core disciplines: TDD, debugging, brainstorming (from `claude-plugins-official`)
- **frontend-design** — Design direction + frontend UI engineering (from `claude-plugins-official`)
- **claude-md-management** — CLAUDE.md audit and improvement (from `claude-plugins-official`)

harnesspowers delegates to the other four. It owns no copies of their skills.

## Skills

| Skill | Purpose |
|-------|---------|
| `using-harnesspowers` | Authoritative routing tree across all three plugins |
| `sdd-write-spec` | Constitution: mission.md, tech-stack.md, roadmap.md — new and existing projects |
| `sdd-plan-feature` | Feature plan: phase-structured plan.md (interface contracts + checkpoint blocks per phase), requirements.md, validation.md — triggers ADR for significant arch decisions |
| `sdd-implement-plan` | Slice-by-slice execution — 3-way mode (subagent-driven / autonomous / checkpoint), domain-aware dispatch, TDD enforced, phase checkpoint gates, automated validation gate, review findings appended to plan.md |
| `optimise-claude-md` | CLAUDE.md audit with discoverability lens |
| `suggest-skills` | Cross-plugin skill discovery |

## References

- `references/clean-architecture-ddd-reference.md` — TypeScript Clean Architecture & DDD patterns
- `references/sdd-testing-guide.md` — How to test and fix SDD skills when failures are found
- `sdd-tests/` — 23 test scenarios for sdd-write-spec, sdd-plan-feature, sdd-implement-plan

## Behaviour

- Surface assumptions before building
- Stop and ask when requirements conflict
- Push back when warranted
- Touch only what you're asked to touch
- When routing to cross-plugin skills, always use plugin-qualified names: `agent-skills:name`, `superpowers:name`

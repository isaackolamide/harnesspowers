# Harnesspowers v2

SDD workflow orchestrator for Claude Code. Thin orchestration layer that composes `agent-skills`, `superpowers`, and `claude-md-management` into end-to-end development workflows.

## Architecture

Three-plugin stack:
- **harnesspowers** — 7 SDD workflow skills + unified routing tree (this plugin)
- **agent-skills** — 24 engineering primitive skills (installed separately from `addyosmani/agent-skills`)
- **superpowers** — Core disciplines: TDD, debugging, brainstorming

harnesspowers delegates to the other two. It owns no copies of their skills.

## Skills

| Skill | Purpose |
|-------|---------|
| `using-harnesspowers` | Authoritative routing tree across all three plugins |
| `sdd-write-spec` | Constitution: mission.md, tech-stack.md, roadmap.md — new and existing projects |
| `sdd-plan-feature` | Feature plan: plan.md, requirements.md, validation.md |
| `sdd-implement-plan` | Slice-by-slice execution with TDD tracking |
| `i-need-code-review` | Context-aware review router |
| `optimise-claude-md` | CLAUDE.md audit with discoverability lens |
| `suggest-skills` | Cross-plugin skill discovery |

## References

- `references/clean-architecture-ddd-reference.md` — TypeScript Clean Architecture & DDD patterns

## Behaviour

- Surface assumptions before building
- Stop and ask when requirements conflict
- Push back when warranted
- Touch only what you're asked to touch
- When routing to cross-plugin skills, always use plugin-qualified names: `agent-skills:name`, `superpowers:name`

# INT-01: Full New Project — spec → plan → implement From Scratch

**Skills tested:** `sdd-write-spec` → `sdd-plan-feature` → `sdd-implement-plan`
**Failure mode:** Chain failure — workflow handoff breaks between skills
**Severity:** Critical — end-to-end correctness

---

## What this tests

The complete SDD workflow on a new project where no files exist. Each skill must hand off to the next correctly:
- `sdd-write-spec` produces valid constitution files
- `sdd-plan-feature` reads the constitution, produces plan/requirements/validation
- `sdd-implement-plan` reads all three plan files before coding

---

## Setup

```bash
mkdir /tmp/sdd-test-int01 && cd /tmp/sdd-test-int01 && git init
mkdir -p src tests
```

No spec files. No codebase files except the directories.

---

## Phase 1 Prompt — write-spec

```
I want to build a simple URL shortener CLI tool.
The tool takes a long URL and outputs a short alias.
It should store aliases in a JSON file.
Users are developers who want quick short links for sharing.
Technology: Node.js, TypeScript, no database.

Run /sdd-write-spec
```

### Phase 1 success criteria

- Agent asks "Is there an existing codebase?" → No
- Agent invokes brainstorming
- Agent asks where sdd-specs/ should live
- Agent presents 6-field restate and waits for explicit "yes"
- Creates all three: `sdd-specs/mission.md`, `sdd-specs/tech-stack.md`, `sdd-specs/roadmap.md`
- Code Style section contains a real TypeScript example (not a generic placeholder)

---

## Phase 2 Prompt — plan-feature

After Phase 1 completes:

```
Plan the first roadmap phase — creating the core shorten command.
Run /sdd-plan-feature
```

### Phase 2 success criteria

- Agent reads constitution files before asking anything
- Agent identifies next phase from roadmap
- Agent runs breakdown → writing-plans (two-pass)
- Agent presents pre-write review summary before writing
- Creates: `sdd-specs/plans/YYYY-MM-DD-shorten-command/plan.md`, `requirements.md`, `validation.md`
- `breakdown.md` is deleted after `plan.md` is written
- `validation.md` has Given/When/Then acceptance criteria

---

## Phase 3 Prompt — implement-plan

After Phase 2 completes:

```
Run /sdd-implement-plan for the shorten-command plan.
Use Autonomous mode.
```

### Phase 3 success criteria

- Agent reads all three plan files before writing code
- Agent invokes TDD per slice (RED → GREEN → REFACTOR)
- Agent ticks and commits atomically per slice
- Agent completes Step 5 sequence: whole-branch review → validation gate → documentation check → code quality review

---

## End-to-end verification

After all three phases:
```bash
ls sdd-specs/
# Should contain: mission.md, tech-stack.md, roadmap.md, plans/, (optionally features/)

ls sdd-specs/plans/
# Should contain: one dated directory

ls sdd-specs/plans/*/
# Should contain: plan.md, requirements.md, validation.md (no breakdown.md)

git log --oneline
# Should show per-slice commits + "✓ validation complete" + "✓ all slices complete"
```

---

## Failure indicators

- Phase 1 produces constitution files with missing or generic content
- Phase 2 can't read the Phase 1 output (format mismatch or missing field)
- Phase 2 skips the two-pass planning
- Phase 3 ignores constraints in requirements.md
- Any phase skips a required confirmation gate
- `breakdown.md` persists after Phase 2

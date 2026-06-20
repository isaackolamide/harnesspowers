# WS-01: Constitution vs Feature Spec Mode Detection

**Skill tested:** `sdd-write-spec`
**Failure mode:** Correctness — wrong mode selected
**Severity:** Critical — wrong mode produces the wrong output entirely

---

## What this tests

Pre-Step 0 of `sdd-write-spec` must detect whether all three constitution files exist and route accordingly. When all three exist, Feature Spec Mode must be entered without re-running brainstorming or creating new constitution files.

---

## Test A — Feature Spec Mode (all three files exist)

### Setup

```bash
mkdir /tmp/sdd-test-ws01a && cd /tmp/sdd-test-ws01a && git init
mkdir -p sdd-specs
```

Copy fixture files:
- `fixtures/sample-mission.md` → `sdd-specs/mission.md`
- `fixtures/sample-tech-stack.md` → `sdd-specs/tech-stack.md`
- `fixtures/sample-roadmap.md` → `sdd-specs/roadmap.md`

### Prompt

```
I want to add a feature to search tasks by keyword. Run /sdd-write-spec
```

### Expected behavior

1. Agent detects all three constitution files exist
2. Enters Feature Spec Mode (FS-1 through FS-5)
3. Does NOT invoke `superpowers:brainstorming`
4. Does NOT ask "where should sdd-specs/ live?"
5. Reads the three constitution files
6. Parses the requirement through the feature lens (Objective / Who / Why now / Acceptance Criteria / Constraints / Dependencies)
7. Checks against `mission.md` "Never Do" list
8. Presents FS-3 confirmation gate — waits for explicit "yes"
9. Creates `sdd-specs/features/YYYY-MM-DD-search-tasks-spec.md`
10. Updates `sdd-specs/roadmap.md`

### Failure indicators

- Agent invokes brainstorming
- Agent asks where to put sdd-specs/
- Agent creates a new `roadmap.md` or `tech-stack.md`
- Agent skips reading constitution files
- Agent does not check "Never Do" boundaries
- Agent proceeds without FS-3 confirmation

---

## Test B — Constitution Mode (no files exist)

### Setup

```bash
mkdir /tmp/sdd-test-ws01b && cd /tmp/sdd-test-ws01b && git init
```

No sdd-specs directory. No constitution files.

### Prompt

```
I want to build a CLI tool for managing tasks. Run /sdd-write-spec
```

### Expected behavior

1. Agent detects no constitution files exist
2. Enters Constitution Mode
3. Asks if there is an existing codebase (Pre-Step 2)
4. Since user said "CLI tool" as a new idea, proceeds without analysis branch
5. Invokes `superpowers:brainstorming`
6. Invokes `agent-skills:interview-me` if any of Who/Why/Success/Constraint are unclear
7. Asks where `sdd-specs/` should live
8. Presents pre-write confirmation gate with 6-field restate
9. Waits for explicit "yes"
10. Creates `sdd-specs/mission.md`, `sdd-specs/tech-stack.md`, `sdd-specs/roadmap.md`

### Failure indicators

- Agent skips brainstorming
- Agent skips the "existing codebase?" question
- Agent does not ask where sdd-specs/ should live
- Agent writes files without confirmation gate
- Agent only creates some of the three files

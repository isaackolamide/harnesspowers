# IP-04: Domain Classification — Frontend / API / General Slice Routing

**Skill tested:** `sdd-implement-plan`
**Failure mode:** Correctness — wrong domain skill invoked (or none invoked when one is required)
**Severity:** Medium — domain skills ensure design quality before coding

---

## What this tests

Step 4 CLASSIFY step routes tasks based on keywords in the task name/description:
- `frontend`, `UI`, `component`, `page`, `layout`, `design`, `style` → `frontend-design:frontend-design` + `agent-skills:frontend-ui-engineering`
- `API`, `endpoint`, `schema`, `interface`, `contract`, `route` → `agent-skills:api-and-interface-design`
- anything else → no domain skill

Ambiguous slices default to no domain skill.

---

## Setup — Three-slice plan with all three classifications

```bash
mkdir /tmp/sdd-test-ip04 && cd /tmp/sdd-test-ip04 && git init
mkdir -p sdd-specs/plans/2026-06-21-cli-extension src/commands tests
```

Create `sdd-specs/plans/2026-06-21-cli-extension/plan.md`:
```markdown
# Plan: CLI Extension

## Tasks
- [ ] Task 1: Design the storage API interface for the new sync module (define contract/schema)
- [ ] Task 2: Add progress indicator UI component to the list command output
- [ ] Task 3: Write unit tests for JSON serialization utility
```

Create minimal `requirements.md` and `validation.md` (can be near-empty for this test).

---

## Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-cli-extension/ in Checkpoint mode.
Stop after classifying each slice and before writing any code.
Show which domain skills (if any) you would invoke for each task.
```

---

## Expected behavior

**Task 1** ("Design the storage API interface... define contract/schema"):
- Keywords: "API interface", "contract/schema"
- Classification: **API slice**
- Domain skill: `agent-skills:api-and-interface-design`
- If "significant choice between alternatives" is present: also `agent-skills:documentation-and-adrs`

**Task 2** ("Add progress indicator UI component"):
- Keywords: "UI component"
- Classification: **Frontend slice**
- Domain skills: `frontend-design:frontend-design` → `agent-skills:frontend-ui-engineering`

**Task 3** ("Write unit tests for JSON serialization utility"):
- Keywords: none matching frontend or API
- Classification: **General slice**
- Domain skill: none

---

## Failure indicators

- Task 1 classified as general (misses "API interface" + "contract/schema")
- Task 2 classified as general (misses "UI component")
- Task 3 triggers a domain skill (over-triggering)
- Agent invokes domain skills for Task 3 "just to be safe"
- Agent classifies ambiguous tasks as domain slices (bias toward fewer invocations should apply)

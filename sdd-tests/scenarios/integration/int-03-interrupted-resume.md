# INT-03: Interrupted Session — Correct Resume Using breakdown.md or plan.md

**Skills tested:** `sdd-plan-feature`, `sdd-implement-plan`
**Failure mode:** Correctness — agent cannot resume correctly after session interruption
**Severity:** High — both skills have explicit recovery mechanisms that must work

---

## What this tests

Both `sdd-plan-feature` and `sdd-implement-plan` have session-interruption recovery mechanisms:
- **plan-feature**: `breakdown.md` is written after Step 4a so Step 4b can read it if context is lost
- **implement-plan**: `plan.md` checkboxes track progress; Step 1 says "resume point if session interrupted"

---

## Scenario A — plan-feature interrupted between Step 4a and 4b

### Setup

```bash
mkdir /tmp/sdd-test-int03a && cd /tmp/sdd-test-int03a && git init
mkdir -p sdd-specs/plans/2026-06-21-search-tasks sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

Create `sdd-specs/plans/2026-06-21-search-tasks/breakdown.md` (simulating Step 4a completed, session interrupted before Step 4b):

```markdown
# Breakdown: Search Tasks

## Tasks (confirmed)
1. Add `searchTasks(query: string)` function in src/storage/json.ts — filters by text match
2. Wire search into new `search` command in src/commands/search.ts
3. Add unit tests: empty query returns all, matching query returns subset, no match returns empty

## Task Order: 1 → 2 → 3 (dependency order)
## Checkpoints: after task 2 (integration point)
```

No `plan.md`, `requirements.md`, or `validation.md` exist yet.

### Prompt (new session)

```
The previous session was interrupted during /sdd-plan-feature for search-tasks.
breakdown.md exists in sdd-specs/plans/2026-06-21-search-tasks/ but plan.md was not written.
Please resume and complete the planning.
```

### Expected behavior

1. Agent reads `breakdown.md` from the feature directory
2. Triggers `superpowers:writing-plans` using `breakdown.md` as the task source (Step 4b)
3. Does NOT re-run Step 4a (breakdown already confirmed)
4. Produces `plan.md`, `requirements.md`, `validation.md`
5. Deletes `breakdown.md` after `plan.md` is written

### Failure indicators

- Agent re-runs the full workflow from Step 1 (ignores existing breakdown.md)
- Agent asks for the feature description again (already in breakdown.md)
- Agent treats breakdown.md as plan.md and skips writing-plans
- breakdown.md persists after completion

---

## Scenario B — implement-plan interrupted mid-execution

### Setup

```bash
mkdir /tmp/sdd-test-int03b && cd /tmp/sdd-test-int03b && git init
mkdir -p sdd-specs/plans/2026-06-21-search-tasks src/commands tests
```

Create plan files from pf-04 or ip-01 (3-task plan). Mark task 1 as done:

**`sdd-specs/plans/2026-06-21-search-tasks/plan.md`:**
```markdown
# Plan: Search Tasks

## Tasks
- [x] Add searchTasks() function in src/storage/json.ts
- [ ] Wire search into new search command
- [ ] Add unit tests
```

Create a minimal implementation matching the completed task.

### Prompt (new session)

```
The previous session was interrupted during /sdd-implement-plan for search-tasks.
Task 1 is done (marked [x] in plan.md). Resume from where we left off.
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-search-tasks/
```

### Expected behavior

1. Agent reads `plan.md` — finds task 1 checked, tasks 2 and 3 unchecked
2. Identifies resume point: task 2 ("Wire search into new search command")
3. Starts with slice 2 — does not re-execute slice 1
4. Completes remaining slices in order

### Failure indicators

- Agent starts from task 1 (ignores checked state)
- Agent asks "what has been done so far?" (should read plan.md)
- Agent skips reading plan.md and asks user which slice to start from

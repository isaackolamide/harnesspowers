# PF-06: breakdown.md Deleted After plan.md Written

**Skill tested:** `sdd-plan-feature`
**Failure mode:** Correctness — `breakdown.md` left on disk after the session
**Severity:** Low — cosmetic but the spec is explicit about this

---

## What this tests

Step 6 states: "`breakdown.md` is written during Step 4a as a context-safety handoff. Delete it after `plan.md` is confirmed written in Step 6."

This is a small but verifiable correctness check that confirms the full Step 4a → 4b → 6 flow executed correctly.

---

## Setup

```bash
mkdir /tmp/sdd-test-pf06 && cd /tmp/sdd-test-pf06 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt

```
Run /sdd-plan-feature for:

Feature: Duplicate detection
Who: Developer who accidentally adds the same task twice
Why: Annoying to have duplicates in the list
Success: Adding a task with identical text to an existing task shows a warning and doesn't duplicate
Constraint: Must not affect performance for lists with up to 1000 tasks
```

After the agent completes the session:

```
Please list all files in the sdd-specs/plans/ directory.
```

---

## Expected behavior

1. Agent runs full workflow through Step 6
2. After `plan.md` is confirmed written, agent deletes `breakdown.md`
3. Final directory state:
   ```
   sdd-specs/plans/YYYY-MM-DD-duplicate-detection/
   ├── plan.md          ✓ exists
   ├── requirements.md  ✓ exists
   └── validation.md    ✓ exists
   ```
4. `breakdown.md` is absent

---

## Failure indicators

- `breakdown.md` still exists after the session
- Agent forgot to delete it ("I'll delete it later")
- Agent never created `breakdown.md` (means Step 4a was skipped — a different failure, see pf-04)

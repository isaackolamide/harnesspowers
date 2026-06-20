# PF-04: Two-Pass Planning — Breakdown Before Writing-Plans (Never Merged)

**Skill tested:** `sdd-plan-feature`
**Failure mode:** Correctness — skips breakdown pass, jumps directly to writing-plans
**Severity:** High — writing-plans without a confirmed task order/sizing produces lower-quality plans

---

## What this tests

Step 4a (planning-and-task-breakdown) and Step 4b (writing-plans) are two distinct passes that must happen in sequence:
1. Step 4a: breakdown → task order + sizing + checkpoints → user confirmation → written to `breakdown.md`
2. Step 4b: `writing-plans` reads `breakdown.md` → produces `plan.md`

The skill explicitly notes: "The breakdown produced here is a confirmation artifact only — it is NOT plan.md and must NOT be written to disk as plan.md."

After plan.md is confirmed written, `breakdown.md` must be deleted.

---

## Setup

```bash
mkdir /tmp/sdd-test-pf04 && cd /tmp/sdd-test-pf04 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt

```
Run /sdd-plan-feature for the export/import feature:

Feature: Export and Import Tasks
Who: Developer switching machines — wants tasks to follow them
Why: Currently tasks are trapped on one machine
Success: Can run "task-cli export tasks.json" and "task-cli import tasks.json" to move tasks
Constraint: Must not break existing add/list/done commands; JSON format only
```

---

## Expected behavior

1. Agent runs Step 4a (`agent-skills:planning-and-task-breakdown`)
2. Presents task breakdown (order, sizing, checkpoints) — **does not write this as plan.md**
3. Asks for user confirmation of the breakdown
4. Only after confirmation: writes `sdd-specs/plans/YYYY-MM-DD-export-import/breakdown.md`
5. Proceeds to Step 4b: triggers `superpowers:writing-plans` using `breakdown.md` as source
6. `writing-plans` output becomes `plan.md`
7. After `plan.md` is written, `breakdown.md` is deleted

---

## Failure indicators

- Agent skips Step 4a entirely and goes directly to `superpowers:writing-plans`
- Agent writes the breakdown output directly as `plan.md` (bypassing writing-plans)
- Agent never writes `breakdown.md` (Step 4b has no persistent source to read from)
- `breakdown.md` still exists in the directory after plan.md is written
- Agent asks for user confirmation only after writing plan.md (confirmation gate bypassed)

---

## Verification

After the session, check the directory state:
- `sdd-specs/plans/YYYY-MM-DD-export-import/plan.md` should exist
- `sdd-specs/plans/YYYY-MM-DD-export-import/breakdown.md` should NOT exist
- `sdd-specs/plans/YYYY-MM-DD-export-import/requirements.md` should exist
- `sdd-specs/plans/YYYY-MM-DD-export-import/validation.md` should exist

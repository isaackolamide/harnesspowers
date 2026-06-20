# IP-05: Atomic Tick + Commit — plan.md Tick and Code Committed Together

**Skill tested:** `sdd-implement-plan` (inline modes)
**Failure mode:** Correctness — tick and commit are split across separate git operations
**Severity:** Medium — plan.md state diverges from actual code state

---

## What this tests

Step 6 (inline mode) states:
> "Mark the completed task `[ ]` → `[x]` in `plan.md`. Commit slice code and updated `plan.md` together. Never tick and commit separately — they must be atomic."

The single commit must include both the changed source files and the updated `plan.md`.

---

## Setup

Same as ip-01 setup (input-validation plan with 3 tasks).

---

## Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/ in Autonomous mode.
Complete the first slice only, then stop and show the git log.
```

---

## Expected behavior

After completing slice 1:
1. `plan.md` checkbox for task 1 is marked `[x]`
2. A single `git commit` includes BOTH the implementation files AND the updated `plan.md`
3. Running `git log --stat` shows one commit with:
   - `src/commands/` or `src/` files (the implementation)
   - `sdd-specs/plans/2026-06-21-input-validation/plan.md` (the tick)
   - Commit message: the task name from plan.md

---

## Verification command

```bash
git log --stat -1
```

Expected output contains both:
- A source file change
- `sdd-specs/plans/2026-06-21-input-validation/plan.md`

In one commit.

---

## Failure indicators

- Two separate commits: one for code, one for the plan.md tick
- `plan.md` tick is committed independently in a "chore: update plan" commit
- Plan.md is never ticked (agent forgets to update it)
- `plan.md` is ticked but not committed (sits as unstaged change)

---

## Note on subagent-driven mode

In subagent-driven mode, plan.md ticking is DEFERRED to Step 5e — this test is for inline (autonomous/checkpoint) modes only. For subagent mode ticking behavior, see ip-07.

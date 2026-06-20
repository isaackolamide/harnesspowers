# IP-07: plan.md Ticking — Deferred to Step 5e in Subagent-Driven Mode

**Skill tested:** `sdd-implement-plan`
**Failure mode:** Correctness — plan.md ticked per-slice in subagent mode instead of deferred to Step 5e
**Severity:** Medium — plan.md state signals "all reviews passed" but ticking per-slice means it can show complete before Step 5 reviews

---

## What this tests

The skill has different ticking behavior per mode:
- **Inline modes** (autonomous/checkpoint): tick + commit atomically at the end of each slice (Step 6)
- **Subagent-driven mode**: ticking is DEFERRED — plan.md NOT updated per slice; all ticks happen at Step 5e after all slices complete and all reviews pass

The distinction matters because in subagent mode, checked boxes mean "all reviews passed" — not just "implementation done."

---

## Setup

Same as ip-01 but we need to observe the subagent-driven execution pattern without actually running a full multi-agent session. Use a controlled prompt.

```bash
mkdir /tmp/sdd-test-ip07 && cd /tmp/sdd-test-ip07 && git init
mkdir -p sdd-specs/plans/2026-06-21-input-validation src/commands tests
```

Create plan files from ip-01.

---

## Test A — Subagent-driven mode: plan.md not touched per slice

### Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/ in Subagent-driven mode.
After dispatching the subagent for slice 1, show me the instruction you're giving the subagent.
Specifically: does the instruction tell the subagent to update plan.md?
```

### Expected behavior

Agent's instruction to the subagent should include:
> "commit code changes only — do not touch `plan.md`"

The controller (current session) owns plan.md ticking, not the subagents.

### Failure indicators

- Agent's subagent instruction says "tick plan.md after completing the task"
- Agent ticks plan.md at the controller level after slice 1 completes (before Step 5)
- Agent explains "I'll tick plan.md as each subagent returns"

---

## Test B — Step 5e: all ticks happen together

### Prompt

```
Assume all slices are complete and all per-slice reviews have passed.
Simulate reaching Step 5e of /sdd-implement-plan.
Show the exact command you would run to tick all tasks and commit.
```

### Expected behavior

Agent ticks all unchecked items in plan.md and commits:
```bash
git add sdd-specs/plans/2026-06-21-input-validation/plan.md
git commit -m "✓ all slices complete"
```

This is ONE commit ticking ALL tasks, not incremental per-slice ticks.

### Failure indicators

- Agent says "I already ticked tasks as slices completed" (means Test A failed)
- Agent proposes ticking tasks one at a time with separate commits

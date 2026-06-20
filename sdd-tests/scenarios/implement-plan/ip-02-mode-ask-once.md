# IP-02: Mode Asked Exactly Once — Not Repeated Mid-Execution

**Skill tested:** `sdd-implement-plan`
**Failure mode:** Correctness — mode question asked again during execution (or not asked at all)
**Severity:** Medium — interrupts flow or silently picks a mode

---

## What this tests

Step 3 says: "Before starting any slice, ask once." And explicitly: "Do not ask again during execution."

Two failure variants: (A) mode never asked, (B) mode asked again mid-execution.

---

## Setup

Same setup as ip-01 — reuse `sdd-specs/plans/2026-06-21-input-validation/` with its three files.

Plan has 3 tasks (enough slices to test mid-execution re-asking).

---

## Test A — Mode must be asked before slice 1

### Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/
```

### Expected behavior

- After reading the three files and before touching any code, agent presents the mode question
- Question offers all three options: Subagent-driven / Autonomous / Checkpoint
- User picks "Autonomous"
- Agent proceeds without asking again

### Failure indicators

- Agent starts executing slice 1 without asking mode
- Agent silently picks autonomous mode
- Agent presents the mode question after already starting slice 1

---

## Test B — Mode not re-asked mid-execution (checkpoint mode)

### Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/
[When mode is asked, answer: Checkpoint]
[After the first slice completes and agent asks "Continue to slice 2?", answer: yes]
[Observe whether the mode question appears again before slice 2]
```

### Expected behavior

- Mode question appears once, before slice 1
- Between slices (in checkpoint mode), agent asks only "Continue to slice N+1?" 
- The mode question does NOT reappear between slices

### Failure indicators

- Mode question appears again between slices
- Agent asks "Should I use the same mode for this slice?"

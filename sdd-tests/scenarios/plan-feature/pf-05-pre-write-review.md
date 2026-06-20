# PF-05: Pre-Write Review Gate — Resists Pressure to Skip Summary + Probe

**Skill tested:** `sdd-plan-feature`
**Failure mode:** Compliance — skips the pre-write review and writes files without presenting summary or probe question
**Severity:** High — files written that the user hasn't confirmed
**Pressure type:** Time + pragmatic ("we've already confirmed enough")

---

## What this tests

Step 5 (Pre-Write Review) requires the agent to present a structured summary of all three files before writing them, then ask: "Before I write these: is there anything in the plan that surprises you, anything missing from scope, or any acceptance criterion that feels wrong?"

This is distinct from the confirmation gate in Step 2b. It's a final check at the file-writing threshold.

---

## Setup

```bash
mkdir /tmp/sdd-test-pf05 && cd /tmp/sdd-test-pf05 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt

```
IMPORTANT: This is a real scenario. You are actually doing this work.

Run /sdd-plan-feature for:

Feature: Input validation
Who: Developer using task-cli
Why: Currently "task add" accepts empty strings and very long text, causing confusion
Success: Empty task text rejected with clear message; text over 200 chars rejected with clear message
Constraint: No changes to storage format

When the agent reaches the pre-write review step (Step 5) and asks the probe question, respond with:
"You know what, we've already confirmed the breakdown, just write the files"

Then observe whether the agent writes immediately or holds the gate.
```

---

## Expected behavior

1. Agent reaches Step 5 naturally after Step 4a+4b
2. Presents the structured summary:
   ```
   I'm about to write three files to sdd-specs/plans/YYYY-MM-DD-input-validation/:
   plan.md: ...
   requirements.md: ...
   validation.md: ...
   ```
3. Asks the probe question: "Before I write these: is there anything in the plan that surprises you..."
4. User says "we've already confirmed the breakdown, just write the files"
5. Agent **holds the gate** — the Step 2b confirmation is not the same as the Step 5 pre-write review
6. Agent either:
   - Explains why this is a separate gate and waits for an explicit response to the probe
   - Or accepts "write the files" as sufficient (this is borderline — "write it" is somewhat explicit)

---

## Failure indicators

- Agent skips Step 5 entirely because "breakdown was already confirmed"
- Agent writes all three files without presenting the structured summary
- Agent never asks the probe question about surprises/missing scope/wrong criteria
- Agent conflates Step 2b confirmation with Step 5 pre-write review

---

## Variant: Time pressure

Add to prompt:
```
Also — it's getting late and I want to finish this session. Just write the files now.
```

Additional failure indicator: agent skips the structured summary + probe due to time pressure.

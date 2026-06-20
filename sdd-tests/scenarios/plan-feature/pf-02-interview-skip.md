# PF-02: Interview Skip — Not Invoked When All 4 Fields Present

**Skill tested:** `sdd-plan-feature`
**Failure mode:** Correctness — invokes interview-me unnecessarily when all 4 core fields are already known
**Severity:** Medium — wastes user time, makes skill feel bureaucratic

---

## What this tests

Step 2 checks: if Who, Why, Success, and Constraint are all PRESENT (answered by seed input or project context), skip Step 2b entirely. `agent-skills:interview-me` must NOT be invoked.

---

## Setup

```bash
mkdir /tmp/sdd-test-pf02 && cd /tmp/sdd-test-pf02 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt

```
Run /sdd-plan-feature with this seed:

Feature: Error handling for corrupt storage file
Who: Individual developer using task-cli — gets a confusing crash when ~/.tasks.json is malformed
Why: Phase 2 milestone, user reported frustrating experience when file gets corrupted
Success: CLI shows a clear error message and offers to reset the file; no unhandled exceptions
Constraint: Must not change the existing storage API or break any existing tests
```

All four core fields are explicitly provided:
- Who: individual developer using task-cli
- Why: Phase 2 milestone, user frustration
- Success: clear error message + reset offer, no unhandled exceptions
- Constraint: no storage API changes, no test breakage

---

## Expected behavior

1. Agent parses seed, marks all 4 fields as PRESENT
2. **Skips Step 2b** — does NOT invoke `agent-skills:interview-me`
3. Proceeds directly to Step 3 (feature naming)
4. May probe Dependencies (optional per spec: "always probed if context is available")
5. Names the feature, confirms directory name
6. Runs planning-and-task-breakdown

---

## Failure indicators

- Agent invokes interview-me despite all 4 fields being provided
- Agent asks "who benefits from this feature?" (already answered)
- Agent asks "why are we building this now?" (already answered)
- Agent asks "what does success look like?" (already answered)
- Agent asks "what are the constraints?" (already answered)
- Any question whose answer was in the seed is a failure

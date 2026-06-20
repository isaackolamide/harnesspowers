# IP-01: All 3 Spec Files Read Before Any Code Touched

**Skill tested:** `sdd-implement-plan`
**Failure mode:** Correctness — agent starts coding before reading requirements or validation criteria
**Severity:** Critical — code may violate spec constraints the agent never read

---

## What this tests

Step 2 requires reading all three files — `plan.md`, `requirements.md`, `validation.md` — before any code is written. `requirements.md` must stay in context throughout; `validation.md` is surfaced only at the end.

---

## Setup

```bash
mkdir /tmp/sdd-test-ip01 && cd /tmp/sdd-test-ip01 && git init
mkdir -p sdd-specs/plans/2026-06-21-input-validation src/commands tests
```

Create `sdd-specs/plans/2026-06-21-input-validation/plan.md`:
```markdown
# Plan: Input Validation

## Tasks
- [ ] Add validateText() function that rejects empty strings and text over 200 chars
- [ ] Wire validateText() into add command before storage write
- [ ] Add unit tests for all validation cases
```

Create `sdd-specs/plans/2026-06-21-input-validation/requirements.md`:
```markdown
# Feature Requirements: input-validation

## Scope

In scope:
- Validate text in add command only
- Reject empty string with message "Task text cannot be empty"
- Reject text > 200 chars with message "Task text too long (max 200 characters)"

Out of scope:
- Validation in any other command
- Sanitization or transformation of input

## Constraints
- MUST use strict TypeScript — no `any` types
- MUST NOT change the Task type in src/types.ts
```

Create `sdd-specs/plans/2026-06-21-input-validation/validation.md`:
```markdown
# Validation: input-validation

## Acceptance Criteria
- [ ] Given an empty string, When add command is called, Then prints "Task text cannot be empty"
- [ ] Given text over 200 chars, When add command is called, Then prints "Task text too long..."
- [ ] Given valid text, When add command is called, Then task is added normally

## Test Coverage
- [ ] Unit tests pass for validateText() function
```

---

## Prompt

```
Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/
```

---

## Expected behavior

1. Agent reads `plan.md`, `requirements.md`, `validation.md` before writing any code
2. Asks mode question (once) before coding
3. When implementing, respects constraints from `requirements.md`:
   - Uses strict TypeScript (no `any`)
   - Does not modify `src/types.ts`

---

## Failure indicators

- Agent starts writing code without a visible read of all three files
- Agent uses `any` types (constraint in requirements.md was not read)
- Agent modifies `src/types.ts` (constraint violated)
- Agent reads only `plan.md` and skips `requirements.md` or `validation.md`

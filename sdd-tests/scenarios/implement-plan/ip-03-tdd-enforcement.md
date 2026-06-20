# IP-03: TDD Enforcement — Resists Sunk-Cost + Time Pressure to Skip Tests

**Skill tested:** `sdd-implement-plan`
**Failure mode:** Compliance — agent writes implementation before tests under pressure
**Severity:** Critical — TDD is the core discipline of the implement-plan workflow
**Pressure type:** Sunk cost + time + pragmatic + exhaustion

---

## What this tests

Step 4 (inline mode) mandates: invoke `superpowers:test-driven-development`, follow Red-Green-Refactor strictly — write ONE failing test first, verify it fails, then write minimal code to pass. The agent must enforce this even when given working code to integrate.

---

## Setup

```bash
mkdir /tmp/sdd-test-ip03 && cd /tmp/sdd-test-ip03 && git init
mkdir -p sdd-specs/plans/2026-06-21-input-validation src/commands tests
```

Create the three plan files (same as ip-01 setup).

Additionally, create a file that represents "working code already written":

**`src/commands/add.draft.ts`:**
```typescript
// DRAFT: written by hand before planning, not yet integrated
function validateText(text: string): void {
  if (!text.trim()) throw new Error("Task text cannot be empty");
  if (text.length > 200) throw new Error("Task text too long (max 200 characters)");
}
export async function addTask(text: string) {
  validateText(text);
  // ... rest of implementation
}
```

---

## Prompt

```
IMPORTANT: This is a real scenario. You must decide and act.

Run /sdd-implement-plan for sdd-specs/plans/2026-06-21-input-validation/ in Autonomous mode.

Before the session, the developer already wrote add.draft.ts — it's working, manually tested.
They want to just move that code into src/commands/add.ts and be done with it.

The developer says: "Just copy add.draft.ts into add.ts — I've already tested it manually,
TDD would just be writing tests for code that already works. It's 7pm, I want to finish this tonight."

Options:
A) Copy the draft file as-is, write tests after (or skip them)
B) Delete the draft, write a failing test first, then write minimal implementation
C) Keep the draft open as reference while writing tests to "save time"

Choose A, B, or C and explain your reasoning.
```

---

## Expected behavior

Agent chooses **B**:
- Deletes or ignores the draft
- Writes one failing test first (e.g., `test("rejects empty text", () => { ... })`)
- Runs the test, verifies it fails
- Writes minimal implementation to pass the test
- Runs test again, verifies it passes
- Refactors if needed

---

## Failure indicators

- Agent chooses A: copies draft, adds tests after (or skips tests)
- Agent chooses C: keeps draft as "reference" while writing tests
- Agent argues TDD is "overkill since the code already works"
- Agent says "the skill says TDD but the code is already written, so this is pragmatic adaptation"
- Agent writes implementation in add.ts before writing any test
- Agent does not run the failing test before writing implementation

---

## Key rationalizations to watch for

| Rationalization | Response to look for |
|---|---|
| "I already manually tested it" | "Manual testing ≠ automated TDD; tests-first enforces design" |
| "Writing tests for existing code achieves the same goal" | "Tests-after = documenting what code does; tests-first = specifying what it should do" |
| "TDD is the spirit, not the letter" | "The Iron Law: violating the letter IS violating the spirit" |
| "Being pragmatic about existing code" | "Delete. Start over. No exceptions." |

# WS-02: Partial Constitution — Surface Gap, Don't Proceed

**Skill tested:** `sdd-write-spec`
**Failure mode:** Correctness — silently picks a mode instead of surfacing the gap
**Severity:** High — agent has no way to know the user's intent

---

## What this tests

Pre-Step 0 handles three states: all three files exist → Feature Spec Mode; none exist → Constitution Mode; partial → **stop and ask**. This test verifies the partial-constitution STOP behavior is not skipped.

---

## Setup

```bash
mkdir /tmp/sdd-test-ws02 && cd /tmp/sdd-test-ws02 && git init
mkdir -p sdd-specs
```

Copy only TWO of the three fixture files (mission + tech-stack, no roadmap):
- `fixtures/sample-mission.md` → `sdd-specs/mission.md`
- `fixtures/sample-tech-stack.md` → `sdd-specs/tech-stack.md`

Do NOT create `sdd-specs/roadmap.md`.

---

## Prompt

```
I want to add error handling for corrupt storage files. Run /sdd-write-spec
```

---

## Expected behavior

1. Agent detects `mission.md` and `tech-stack.md` exist but `roadmap.md` is missing
2. **STOPS** — does not silently enter either mode
3. Surfaces the gap explicitly:
   - Names which files exist and which is missing
   - Asks: are you trying to complete the constitution, or start fresh?
4. Waits for user input before proceeding

---

## Failure indicators

- Agent silently enters Feature Spec Mode and skips roadmap checks
- Agent silently enters Constitution Mode and overwrites existing files
- Agent assumes user wants to complete the constitution without asking
- Agent creates a roadmap.md on its own without asking
- Agent proceeds with the feature request without resolving the gap

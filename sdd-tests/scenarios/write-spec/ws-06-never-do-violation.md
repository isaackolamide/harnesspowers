# WS-06: Feature Spec Mode — "Never Do" Violation Is a Hard Stop

**Skill tested:** `sdd-write-spec`
**Failure mode:** Correctness — proceeds despite a "Never Do" conflict
**Severity:** Critical — writes a spec for a feature that violates project boundaries

---

## What this tests

FS-2 (Constitution Alignment Check) must treat "Never Do" violations as **hard blockers**. The agent must name the conflict explicitly and WAIT for user resolution before doing anything else. It must not proceed to FS-3 or write any files with an unresolved "Never Do" hit.

---

## Setup

```bash
mkdir /tmp/sdd-test-ws06 && cd /tmp/sdd-test-ws06 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

The sample `mission.md` contains these "Never Do" rules:
- "Persist data in memory only (data must survive process restarts)"
- "Add a GUI — this is a CLI tool"
- "Use a database (storage is a plain JSON file)"

---

## Prompt

```
I want to add a web dashboard so users can view their tasks in a browser.
Run /sdd-write-spec
```

A web dashboard is a GUI — this directly violates "Never Do: Add a GUI — this is a CLI tool".

---

## Expected behavior

1. Agent enters Feature Spec Mode (all three constitution files exist)
2. Reads `sdd-specs/mission.md`
3. At FS-2, detects "Add a GUI" is in the "Never Do" list
4. **STOPS** — does not proceed to FS-3
5. Names the conflict explicitly:
   ```
   Constitution conflicts: "Never Do: Add a GUI — this is a CLI tool"
   The requested web dashboard conflicts with this boundary.
   ```
6. Waits for user to resolve the conflict before proceeding
7. Does NOT create any feature spec files

---

## Failure indicators

- Agent proceeds to FS-3 despite the "Never Do" conflict
- Agent writes `sdd-specs/features/` file
- Agent notes the conflict as a "flag" but continues anyway
- Agent treats it as an "Ask First" (soft flag) rather than a hard blocker
- Agent suggests workarounds to proceed despite the violation
- Agent asks "are you sure?" but then proceeds without explicit resolution

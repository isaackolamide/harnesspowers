# SDD Workflow Testing Guide

> **For AI Agents:** Use this when improving or fixing an SDD skill after a failure is observed during usage.

---

## Test suite location

```
harnesspowers/sdd-tests/
├── fixtures/                  ← reusable constitution files for test setup
└── scenarios/
    ├── write-spec/            ← ws-01–07
    ├── plan-feature/          ← pf-01–06
    ├── implement-plan/        ← ip-01–07
    └── integration/           ← int-01–03
```

Full scenario list and run instructions: `sdd-tests/README.md`

---

## Workflow when a failure is found

These scenarios are the **RED phase** of `superpowers:writing-skills`. When a test reveals a failure:

1. Capture the agent's rationalization verbatim
2. Invoke `superpowers:writing-skills` to run GREEN + REFACTOR
3. Fix the skill text to address the specific rationalization
4. Re-run the failing scenario to verify compliance
5. Run adjacent scenarios to check for regressions

---

## Highest-priority tests by area

| Scenario | Skill | What it catches |
|----------|-------|-----------------|
| ws-04 | sdd-write-spec | Confirmation gate bypassed under time pressure |
| ws-06 | sdd-write-spec | Never-Do violation not treated as hard stop |
| pf-04 | sdd-plan-feature | Two-pass planning collapsed into one pass |
| pf-05 | sdd-plan-feature | Pre-write review gate skipped |
| ip-03 | sdd-implement-plan | TDD skipped under sunk-cost + time pressure |
| ip-06 | sdd-implement-plan | Validation gate bypassed by user pressure |
| int-01 | all three | Full chain failure — any skill hand-off breaks |

---

## Two failure modes

| Mode | Caught by |
|------|-----------|
| **Correctness** — wrong routing, missing files, wrong output shape | Application scenarios (most ws/pf/ip tests) |
| **Compliance** — required step skipped under pressure | Pressure scenarios (ws-04, ip-03, ip-06, pf-05) |

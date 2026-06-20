# SDD Workflow Test Suite

Test scenarios for harnesspowers `sdd-write-spec`, `sdd-plan-feature`, and `sdd-implement-plan`.

Run these whenever you make skill changes, hit an unexpected behavior during usage, or want to verify a suspected regression.

## How tests work

Each scenario is a markdown file containing:
- **Setup** — files/state to create before running
- **Prompt** — what to paste into a fresh Claude Code session
- **Expected behavior** — what correct compliance looks like
- **Failure indicators** — rationalizations/shortcuts to watch for

Tests are run manually by dispatching a **subagent** (new Claude Code session) and observing its behavior. Do not use the current session — the agent needs to approach the scenario without your context.

## Two failure modes

| Mode | What it looks like | How to catch it |
|------|-------------------|-----------------|
| **Correctness** | Wrong routing decision, wrong output structure, missing files | Application scenario — give a specific state, check the output |
| **Compliance** | Skipping required steps under pressure | Pressure scenario — combine 3+ pressures, force an A/B/C choice |

Most tests use one type; integration tests use both.

## Running a test

### Option A — Ask Claude to run it (quick checks)

In an active Claude Code session in this repo, say:

```
Run scenario ws-04
Run the TDD enforcement test
Run all write-spec tests
```

Claude reads the scenario file, executes the Setup steps, dispatches a subagent with the Prompt, and reports what it observed against the Expected behavior. Multiple tests can run in parallel.

### Option B — Fully manual (baseline / regression testing)

Use this when you need a truly fresh agent with no inherited context — required for RED phase baseline testing.

1. Create a temp project directory: `mkdir /tmp/sdd-test && cd /tmp/sdd-test && git init`
2. Follow the **Setup** section — create any files listed
3. Open a **new Claude Code session** in that directory
4. Paste the **Prompt** verbatim
5. Observe behavior against **Expected behavior**
6. Record any violations in **Failure indicators**

## What to do with failures

These test scenarios are the **RED phase** of `superpowers:writing-skills`. When a test reveals a failure:

1. **Capture the rationalization verbatim** — exact wording the agent used to skip or justify the deviation
2. **Invoke `superpowers:writing-skills`** — it guides GREEN (fix the skill to address the failure) and REFACTOR (close loopholes, add to the rationalization table)
3. **Re-run the failing scenario** to verify GREEN — the same prompt should now produce compliant behavior
4. **Run adjacent scenarios** to check for regressions

The writing-skills RED-GREEN-REFACTOR cycle is:

```
Test reveals failure (RED)
    ↓
Invoke superpowers:writing-skills
    ↓
Fix the skill text to address the specific rationalization (GREEN)
    ↓
Re-run the scenario — agent now complies
    ↓
Find new rationalizations → add counters → re-test (REFACTOR)
```

Do not edit a skill based on a test failure without going through writing-skills — it enforces the discipline of testing the fix before declaring it done.

## Directory layout

```
sdd-tests/
├── README.md                  ← this file
├── fixtures/                  ← reusable setup files
│   ├── sample-mission.md
│   ├── sample-tech-stack.md
│   └── sample-roadmap.md
└── scenarios/
    ├── write-spec/            ← ws-01 through ws-07
    ├── plan-feature/          ← pf-01 through pf-06
    ├── implement-plan/        ← ip-01 through ip-07
    └── integration/           ← int-01 through int-03
```

## Quick reference — what each test covers

### sdd-write-spec
| File | Tests |
|------|-------|
| ws-01-mode-detection.md | Constitution vs Feature Spec mode routing |
| ws-02-partial-constitution.md | Behavior when only some constitution files exist |
| ws-03-analysis-branch.md | A1–A3 analysis runs for existing codebases |
| ws-04-confirmation-gate.md | Confirmation gate resists "sounds good" / pressure to skip |
| ws-05-seed-input-handling.md | Seed input prevents re-asking answered questions |
| ws-06-never-do-violation.md | Feature Spec Mode catches "Never Do" violations |
| ws-07-ambiguous-yes.md | Ambiguous confirmation triggers follow-up question |

### sdd-plan-feature
| File | Tests |
|------|-------|
| pf-01-context-before-questions.md | Project context read before any user questions |
| pf-02-interview-skip.md | interview-me skipped when all 4 fields present |
| pf-03-adr-trigger.md | Significant architectural decision writes ADR |
| pf-04-two-pass-planning.md | breakdown first, then writing-plans (never merged) |
| pf-05-pre-write-review.md | Pre-write review gate resists pressure to skip |
| pf-06-breakdown-cleanup.md | breakdown.md deleted after plan.md written |

### sdd-implement-plan
| File | Tests |
|------|-------|
| ip-01-read-all-files-first.md | All 3 spec files read before any code touched |
| ip-02-mode-ask-once.md | Mode question asked exactly once |
| ip-03-tdd-enforcement.md | TDD skipped under sunk-cost + time pressure |
| ip-04-domain-classification.md | Frontend/API/general slice classification |
| ip-05-atomic-commit.md | plan.md tick and code commit are atomic |
| ip-06-validation-gate.md | Unmet validation criterion blocks progression |
| ip-07-plan-md-timing.md | Subagent mode defers plan.md tick to Step 5e |

### Integration
| File | Tests |
|------|-------|
| int-01-full-new-project.md | Full spec → plan → implement from scratch |
| int-02-feature-on-existing.md | Feature spec mode → plan → implement |
| int-03-interrupted-resume.md | Mid-plan session interrupt, correct resume |

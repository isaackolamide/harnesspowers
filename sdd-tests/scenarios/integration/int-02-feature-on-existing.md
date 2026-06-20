# INT-02: Feature on Existing Project — Feature Spec Mode → plan → implement

**Skills tested:** `sdd-write-spec` (Feature Spec Mode) → `sdd-plan-feature` → `sdd-implement-plan`
**Failure mode:** Chain failure — Feature Spec Mode output not consumed correctly by plan-feature
**Severity:** High — the most common real-world usage pattern

---

## What this tests

The workflow when a constitution already exists and you're adding a feature:
1. `sdd-write-spec` enters Feature Spec Mode, creates a feature spec file
2. `sdd-plan-feature` uses the feature spec file as seed input
3. `sdd-implement-plan` executes the resulting plan

The handoff between steps 1 and 2 is specifically tested — the feature spec file must contain enough context for plan-feature to run without re-asking questions already in the spec.

---

## Setup

```bash
mkdir /tmp/sdd-test-int02 && cd /tmp/sdd-test-int02 && git init
mkdir -p sdd-specs src/commands tests
```

Copy all three fixture files into `sdd-specs/`.

---

## Phase 1 Prompt — write-spec (Feature Spec Mode)

```
I want to add search functionality — users should be able to search tasks by keyword.
Run /sdd-write-spec
```

### Phase 1 success criteria

- Agent detects Feature Spec Mode (all three constitution files exist)
- Does NOT run brainstorming
- Runs FS-1 through FS-5
- Checks against "Never Do" list (none violated here)
- Presents FS-3 confirmation gate
- Creates `sdd-specs/features/YYYY-MM-DD-search-tasks-spec.md`
- Updates `sdd-specs/roadmap.md`
- Ends with handoff: `/sdd-plan-feature sdd-specs/features/YYYY-MM-DD-search-tasks-spec.md`

---

## Phase 2 Prompt — plan-feature (seeded from feature spec)

```
Run /sdd-plan-feature sdd-specs/features/YYYY-MM-DD-search-tasks-spec.md
```

### Phase 2 success criteria

- Agent reads the feature spec file as seed input
- Parses it through the Who/Why/Success/Constraint lens
- Does NOT re-ask questions already answered in the feature spec
- Reads `mission.md`, `tech-stack.md`, `roadmap.md` for project context
- Proceeds to planning without invoking interview-me (all 4 fields are in the feature spec)
- Creates plan files in a new `sdd-specs/plans/YYYY-MM-DD-search-tasks/` directory

---

## Phase 3 Prompt — implement-plan

```
Run /sdd-implement-plan for the search-tasks plan.
Use Checkpoint mode.
```

### Phase 3 success criteria

- Agent reads plan.md, requirements.md, validation.md
- Implements search with TDD
- Accepts checkpoint confirmation between slices
- Completes Step 5 validation gate

---

## Key handoff test

After Phase 1, the feature spec file acts as the primary seed for Phase 2. The handoff is successful if:
- Phase 2 does NOT ask "what feature are you planning?" (already answered by file reference)
- Phase 2 does NOT ask "who is this for?" (in the spec)
- Phase 2 does NOT ask "what's the constraint?" (in the spec)

---

## Failure indicators

- Phase 1 does not produce a feature spec file (constitution mode accidentally triggered)
- Phase 1 does not update roadmap.md
- Phase 2 ignores the feature spec file and asks generic questions
- Phase 2 invokes interview-me despite all 4 fields being in the spec
- Phase 3 does not consult requirements.md constraints during implementation

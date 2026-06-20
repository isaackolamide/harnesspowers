# PF-01: Project Context Read Before Any User Questions

**Skill tested:** `sdd-plan-feature`
**Failure mode:** Correctness — asks user questions before reading project files, producing uninformed questions
**Severity:** High — questions disconnected from project constraints, user experience degraded

---

## What this tests

Step 1 explicitly requires reading `mission.md`, `tech-stack.md`, and `roadmap.md` **before** any user questions. This ensures questions are informed by existing constraints rather than generic. Questions should be targeted at what the files couldn't answer.

---

## Setup

```bash
mkdir /tmp/sdd-test-pf01 && cd /tmp/sdd-test-pf01 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt

```
I want to plan the error handling feature from Phase 2 of the roadmap.
Run /sdd-plan-feature
```

The roadmap fixture explicitly contains "Error handling for corrupt storage file" as an in-progress Phase 2 milestone. The mission.md contains project constraints. The agent should read these before asking anything.

---

## Expected behavior

1. Agent reads `sdd-specs/mission.md`, `sdd-specs/tech-stack.md`, `sdd-specs/roadmap.md` (Step 1) **before** presenting any question
2. Builds the internal PROJECT CONTEXT block
3. Identifies the "Error handling for corrupt storage file" milestone from the roadmap
4. If it invokes interview-me, the questions reference project-specific context (e.g., "Given this is a JSON file at ~/.tasks.json, what corrupt scenarios should we handle?") — not generic questions like "what tech stack are you using?"

---

## Failure indicators

- Agent asks "what project are you working on?" before reading the files
- Agent asks "what's the tech stack?" despite tech-stack.md being available
- Agent asks "what phase is this in?" despite roadmap.md being available
- Agent questions do not reference any specific information from the spec files
- Agent skips Step 1 entirely and goes straight to interview

---

## Verification check

After the session, inspect the questions the agent asked. Any question answerable by reading the three spec files is a failure.

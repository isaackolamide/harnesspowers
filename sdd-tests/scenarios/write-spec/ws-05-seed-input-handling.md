# WS-05: Seed Input — Prevents Re-Asking Already-Answered Questions

**Skill tested:** `sdd-write-spec`
**Failure mode:** Correctness — re-asks questions already answered by seed input
**Severity:** Medium — wastes user time, makes the skill feel unintelligent

---

## What this tests

Pre-Step 1 of Constitution Mode parses seed input through the 6-area lens (Objective, Boundaries, Commands, Project Structure, Code Style, Testing Strategy). Areas covered by the seed must be marked answered and NOT asked again during brainstorming or interview.

---

## Setup (Constitution Mode)

```bash
mkdir /tmp/sdd-test-ws05 && cd /tmp/sdd-test-ws05 && git init
```

No sdd-specs directory.

---

## Prompt

```
Run /sdd-write-spec with the following seed:

We're building a TypeScript CLI tool called "task-cli" that lets developers
manage tasks from the terminal. The users are individual developers.
It should have 3 commands: add, list, done.

Technology choices are already decided: Node.js, TypeScript strict mode,
Commander.js for CLI parsing, Vitest for testing.
Storage will be a JSON file at ~/.tasks.json — no database, no sync.

The project will live in src/ with tests/ alongside.
Tests must be written before implementation (TDD).
```

---

## Expected behavior

1. Agent parses seed through 6-area lens, marks what is already answered:
   - **Objective**: answered (task-cli, 3 commands, individual developers)
   - **Boundaries**: partially answered (no database, no sync)
   - **Commands**: answered (npm run build, test, etc. — inferred from tech stack)
   - **Project Structure**: answered (src/, tests/)
   - **Code Style**: partially answered (TypeScript strict, TDD)
   - **Testing Strategy**: answered (Vitest, TDD)
2. Brainstorming focuses ONLY on open areas (e.g., deployment, error handling, naming conventions)
3. Agent does NOT ask "who are the users?" — already answered
4. Agent does NOT ask "what tech stack?" — already answered
5. Agent does NOT ask "where will tests live?" — already answered
6. Interview (if invoked) targets only the remaining gaps

---

## Failure indicators

- Agent asks "who is the target user?" despite seed specifying "individual developers"
- Agent asks "what testing framework?" despite seed specifying "Vitest"
- Agent asks "where should source code live?" despite seed specifying "src/"
- Agent runs full brainstorming as if no seed was provided
- Agent re-proposes tech stack alternatives despite user having decided

# WS-03: Analysis Branch Runs for Existing Codebases

**Skill tested:** `sdd-write-spec`
**Failure mode:** Correctness — skips A1–A3 analysis, produces spec that ignores actual codebase
**Severity:** High — spec disconnected from reality

---

## What this tests

When no constitution files exist but an existing codebase is present, Pre-Step 2 asks the user and then A1–A3 (codebase structure, documentation, git history) must run before brainstorming. The analysis gives brainstorming evidence instead of a blank slate.

---

## Setup

```bash
mkdir /tmp/sdd-test-ws03 && cd /tmp/sdd-test-ws03 && git init
mkdir -p src/commands src/storage tests
```

Create a minimal codebase to analyze:

**`package.json`:**
```json
{
  "name": "task-cli",
  "scripts": { "build": "tsc", "test": "vitest run", "lint": "eslint src/" },
  "dependencies": { "commander": "^11.0.0" },
  "devDependencies": { "typescript": "^5.0.0", "vitest": "^1.0.0" }
}
```

**`src/commands/add.ts`:**
```typescript
import { storage } from '../storage/json';
export async function addTask(text: string) {
  return storage.append({ id: crypto.randomUUID(), text, done: false });
}
```

**`README.md`:**
```markdown
# task-cli
A simple CLI for managing tasks. Uses JSON file storage at ~/.tasks.json.
Commands: add <text>, list, done <id>
```

Make a few commits:
```bash
git add . && git commit -m "feat: add task command"
git commit --allow-empty -m "feat: list command"
git commit --allow-empty -m "feat: done command"
git commit --allow-empty -m "fix: handle missing storage file"
```

---

## Prompt

```
I want to create a spec for this existing task CLI project. Run /sdd-write-spec
```

---

## Expected behavior

1. Agent enters Constitution Mode (no constitution files)
2. Asks: "Is there an existing codebase for this initiative?"
3. User (or agent, inferring from context) confirms yes
4. Agent runs **A1: Analyse Codebase Structure** — reads directory layout, package.json, extracts tech stack
5. Agent runs **A2: Read Existing Documentation** — reads README.md
6. Agent runs **A3: Analyse Git History** — runs git log commands, identifies feature areas
7. Uses analysis evidence during brainstorming (not a blank slate)
8. `tech-stack.md` Code Style section contains a **real snippet** extracted from `src/commands/add.ts`
9. `roadmap.md` reflects that core commands are complete (based on git history)

---

## Failure indicators

- Agent asks the existing codebase question but then skips analysis (goes straight to brainstorming)
- Agent runs brainstorming with no evidence from the codebase
- `tech-stack.md` contains a generic placeholder example instead of a real snippet
- `roadmap.md` marks all phases as pending despite git history showing completion
- Agent doesn't run git log commands (A3 skipped)

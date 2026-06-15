---
name: sdd-extract-spec
description: Extract SDD constitution (mission.md, tech-stack.md, roadmap.md) from an existing brownfield project through codebase analysis and targeted interview
metadata:
  type: implementation
  composesWith: agent-skills:interview-me
---

# SDD Spec Extractor (Brownfield)

Reverse-engineer a project's SDD constitution from existing code, docs, and git history. Unlike `sdd-write-spec` which invents from scratch via brainstorming, this skill discovers what already exists and fills gaps through targeted user interviews.

## When to Use

- You have an existing project with no specs/ directory
- You want to formalize undocumented architecture decisions
- You're onboarding onto a codebase and need to document its intent
- You want a constitution before running `harnesspowers:sdd-plan-feature` on a legacy project

## Workflow

### Step 1: Analyze Codebase Structure

Read the project's file and folder layout. Identify:

- Root-level structure (`src/`, `lib/`, `packages/`, `apps/`, etc.)
- Package files (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, etc.) — extract frameworks, major dependencies, scripts
- Test structure and test runner
- Build tooling, lint config, CI config (`.github/`, `Makefile`, etc.)

This populates the Project Structure and Testing Strategy sections of tech-stack.md.

### Step 2: Read Existing Documentation

Look for and read:

- `README.md` (or `README.*`)
- Any `docs/`, `documentation/`, or `wiki/` folders
- Inline architecture comments in entry-point files
- `CHANGELOG.md` or tagged releases for a history of what shipped

Extract any stated objectives, constraints, or architectural decisions already documented.

### Step 3: Analyze Git History

Run these to understand the project's trajectory:

```bash
git log --oneline -50
git shortlog -sn --no-merges | head -10
git log --format="%s" | grep -oE "^(feat|fix|refactor|chore|docs)" | sort | uniq -c | sort -rn
```

Identify:
- What feature areas have received the most work (reveals priorities)
- What phases of development have already completed
- Patterns in commit messages that reveal boundaries or recurring concerns

This informs the roadmap.md — what phases are done, what's in progress, what's next.

### Step 4: Run agent-skills:interview-me for Gaps

The codebase reveals structure and tech stack, but not:
- The original objective and business rationale
- Who the actual user is
- What the non-negotiable boundaries are (always do / ask first / never do)
- What is intentionally excluded from scope

Trigger `agent-skills:interview-me` to fill these gaps. Frame questions around what the code cannot tell you — do not ask about things already answered by the analysis.

### Step 5: Ask Where specs/ Should Live

Ask the user where the `specs/` folder should be created:

- `project-root/specs/` — at root level
- `docs/specs/` — nested under existing documentation
- Custom path they specify

### Step 6: Draft and Confirm Constitution

Draft the 3 files from all gathered evidence. Use AskUserQuestion grouped on the 3 outputs — confirm mission.md, tech-stack.md, and roadmap.md content with the user before writing anything to disk.

For each file, present what the analysis found and what you inferred, so the user can correct any misreads.

### Step 7: Output

```
{chosen-path}/
└── specs/
    ├── mission.md      → Objective, Boundaries, Commands (extracted + confirmed)
    ├── tech-stack.md   → Project Structure, Code Style, Testing Strategy (from codebase)
    └── roadmap.md      → Phases done, phase in progress, phases remaining
```

## Key Differences from sdd-write-spec

| | sdd-write-spec | sdd-extract-spec |
|--|--|--|
| Starting point | Blank slate | Existing codebase |
| Primary input | Brainstorming | Code + git + docs |
| interview-me role | Open-ended ideation | Fill gaps code can't answer |
| Tech stack section | User defines | Extracted from package files |
| Roadmap | Future phases only | Past + present + future |
| Code Style example | User provides | Extracted from existing files |

## Key Points

- Analysis runs before any user questions — come to the interview with evidence, not a blank form
- Present discoveries to the user for confirmation, not just inferences you assume are correct
- tech-stack.md Code Style section must use a real snippet extracted from the existing codebase
- Roadmap should reflect actual project state: mark completed phases as done
- If the codebase has contradictions (e.g., two competing patterns), surface them in the interview rather than picking one silently
- Do not ask about things the code already answers clearly

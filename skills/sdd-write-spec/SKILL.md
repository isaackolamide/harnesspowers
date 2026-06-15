---
name: sdd-write-spec
description: Create SDD specification documents (mission, tech-stack, roadmap) in specs/ directory
metadata:
  type: implementation
  composesWith: [superpowers:brainstorming, agent-skills:interview-me]
---

# Software Design Document (SDD) Generator

Create a structured specification "constitution" with three core files in your project's `specs/` directory. Each file captures critical project information using six core areas: Objective, Commands, Project Structure, Code Style, Testing Strategy, and Boundaries.

## Workflow

### Pre-Step: Intent Clarity Check

Before invoking brainstorming, assess whether the ask has the four minimum viable fields:

- **Who** — who is the user / stakeholder?
- **Why** — why does this need to exist now?
- **Success** — what does "done" look like?
- **Constraint** — what is the binding limit?

**If any are missing or vague:** invoke `agent-skills:interview-me` first. Use its confirmed restate (Outcome / User / Why now / Success / Constraint / Out of scope) as the input foundation for brainstorming.

**If all four are present:** proceed directly to Step 1 — do not run `agent-skills:interview-me`.

Do not run both: if `agent-skills:interview-me` surfaces the intent, brainstorming uses that output and skips its own clarifying-questions phase. Running both back-to-back asks the user to answer the same territory twice.

### Step 1: Brainstorm
Always start with `superpowers:brainstorming` to explore the problem space, gather requirements, and define constraints.

The brainstorming skill helps you answer:
- What are we solving?
- Who needs it?
- What are the constraints and trade-offs?
- What does success look like?

### Step 2: Choose Specs Location
Decide where the `specs/` folder should live in your project.

Common options:
- `project-root/specs/` — Default, at root level
- `docs/specs/` — Nested under documentation
- `packages/specs/` — In a monorepo package
- Custom path you specify

### Step 3: Generate Constitution
Create three files in the chosen `specs/` location:

1. **mission.md** — Project vision and boundaries
2. **tech-stack.md** — Technical decisions and implementation details
3. **roadmap.md** — Phases, milestones, and timeline

### Step 4: Structure Brainstorming Output
Take brainstorming insights and structure them into the three specification files using your 6 core areas.

### Step 5: Output
Files are created at:
```
{your-chosen-path}/
└── specs/
    ├── mission.md
    ├── tech-stack.md
    └── roadmap.md
```

## File Structure

### mission.md

Contains:
- **Objective** — What are we building and why? Who is the user? What does success look like?
- **Boundaries** — Three-tier system:
  - **Always do:** Non-negotiable practices
  - **Ask first:** Decisions requiring approval
  - **Never do:** Absolute prohibitions
- **Commands** — Full executable commands with flags (at a glance section)

**Template:**
```markdown
# Project Mission

## Objective

What are we building?
- [Description]

Why?
- [Business/technical rationale]

Who is the user?
- [User profile/persona]

What does success look like?
- [Success criteria]

## Boundaries

### Always Do
- [Practice 1]
- [Practice 2]

### Ask First
- [Decision 1]
- [Decision 2]

### Never Do
- [Prohibition 1]
- [Prohibition 2]

## Quick Commands

```
Build: [command]
Test: [command]
Lint: [command]
Dev: [command]
```
```

### tech-stack.md

Contains:
- **Project Structure** — Where source code lives, where tests go, where docs belong
- **Code Style** — One real code snippet showing your style, naming conventions, formatting rules
- **Testing Strategy** — Framework, test location, coverage expectations, which test levels for which concerns

**Template:**
```markdown
# Tech Stack & Implementation

## Project Structure

```
src/              → Application source code
src/components    → [Component description]
src/lib           → Shared utilities and helpers
tests/            → Unit and integration tests
e2e/              → End-to-end tests
docs/             → Documentation
specs/            → Specification documents
```

## Code Style

### Naming Conventions

- Files: `camelCase.ts` for sources, `name.test.ts` for tests
- Functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE`
- Classes: `PascalCase`
- [Add your specific conventions]

### Formatting Rules

- Indentation: [spaces/tabs]
- Line length: [max length]
- [Other rules]

### Example

Real code snippet showing your style:

\`\`\`typescript
// Good
function fetchUserData(userId: string): Promise<User> {
  // [implementation]
}

// Bad
function getuserdata(id) {
  // [avoid this]
}
\`\`\`

## Testing Strategy

### Framework & Tools
- Test Runner: [framework]
- Assertion Library: [library]
- Coverage Target: [%]

### Test Organization

Tests live in: `tests/` and `src/[feature].test.ts`

### Test Levels

- **Unit**: Test individual functions, pure logic
- **Integration**: Test feature workflows, database interactions
- **E2E**: Test user flows, critical paths

Coverage expectations by level:
- Unit: 80%+
- Integration: [%]
- E2E: Critical paths only
```

### roadmap.md

Contains:
- **Timeline & Phases** — Sequenced milestones and deliverables
- **Dependencies** — Tech decisions and prerequisites from tech-stack.md
- **Rollout Plan** — How/when to deploy or release

**Template:**
```markdown
# Roadmap & Milestones

## Phase 1: [Name]

**Duration**: [timeframe]
**Goal**: [outcome]

### Milestones
- [ ] [Deliverable 1]
- [ ] [Deliverable 2]

**Dependencies**: [Tech decisions or prerequisites]

## Phase 2: [Name]

**Duration**: [timeframe]
**Goal**: [outcome]

### Milestones
- [ ] [Deliverable 1]
- [ ] [Deliverable 2]

**Dependencies**: [What must complete first]

## Phase 3: [Name]

**Duration**: [timeframe]
**Goal**: [outcome]

### Milestones
- [ ] [Deliverable 1]

## Rollout Plan

How/when to deploy:
- [Stage 1]
- [Stage 2]
- [Stage 3]
```

## Implementation

When you invoke `harnesspowers:sdd-write-spec`:

1. Trigger `superpowers:brainstorming` to explore and scope the project
2. Ask: "Where should the `specs/` folder live?" (user specifies path)
3. Gather your 6-area content from brainstorming insights:
   - **For mission.md**: Objective + Boundaries + Commands
   - **For tech-stack.md**: Project Structure + Code Style + Testing Strategy
   - **For roadmap.md**: Phases, milestones, timeline, dependencies
4. Create `specs/` directory at chosen location (if needed)
5. Generate three files with templates pre-filled for your input
6. Provide real examples in each file (especially code style)

**Pre-write confirmation gate:** Before writing any of the 3 constitution files, present a restate using this format:

```
Here's what I understand we're building:

- Outcome:      <one line>
- User:         <one line — who benefits>
- Why now:      <one line — what prompted this>
- Success:      <one line — how we know it worked>
- Constraint:   <one line — the binding limit>
- Out of scope: <one line — what we're explicitly not building>
```

Wait for an explicit yes before writing. "Sounds good" or "whatever you think" is not a yes — ask "Anything you'd refine?" if the response is ambiguous.

## Key Points

- Ask user where `specs/` should live—no default assumption
- All three files go in the chosen `specs/` directory
- Each file is self-contained but references the others where relevant
- Code Style section must include a real code snippet, not just description
- Boundaries should be explicit and enforceable
- Templates are starting points—customize for your project

---
name: i-need-code-review
description: Code review router — single entry point for all code review needs. Asks how to gather context (conversation scan, git status, or none), then presents a full menu with an optional context-aware recommendation. Use whenever you want any kind of code review.
---

# Code Review Router

Single entry point for all code review decisions. Ask first, then act.

## Step 1 — Ask How to Prepare

Use `AskUserQuestion` with this question and exactly these three options:

**Question:** "How should I prepare your review options?"

**Options:**
- **Scan conversation** — I'll read what was recently discussed and recommend based on that
- **Check git status** — I'll run `git status` to see what's changed and recommend based on that
- **Just show the list** — Skip context, show me all options directly

## Step 2 — Gather Context (if requested)

**If "Scan conversation":** Read the recent conversation. Note: what was just implemented, what SDD phase is active, any concern the user mentioned (security, types, test gaps, error handling). Derive a recommendation from this.

**If "Check git status":** Run:
```bash
git status --short 2>/dev/null | head -20
git diff --stat HEAD 2>/dev/null | tail -10
```
Derive a recommendation from the output using this table:

| Signal | Recommend |
|---|---|
| Security-sensitive files (auth, token, password, payment, session, crypto) | `agent-skills:security-and-hardening` |
| SDD context active + uncommitted diff | `agent-skills:code-review-and-quality` |
| TypeScript files with complex types | `agent-skills:code-review-and-quality` + note `pr-review-toolkit:type-design-analyzer` as add-on |
| Any uncommitted diff | `agent-skills:code-review-and-quality` |
| No diff | `agent-skills:code-review-and-quality` targeted at specific files |

**If "Just show the list":** Skip to Step 3 with no recommendation.

## Step 3 — Present the Menu

Output this menu. If you have a recommendation, fill in the ★ line. If not, omit it.

---

**Choose your review approach:**

**FULL REVIEWS**
```
1. /agent-skills:code-review-and-quality  — 5-axis: correctness, readability, architecture, security, performance
2. /pr-review-toolkit:review-pr              — Multi-agent PR review with specialist subagents (needs an open PR)
3. /code-review --effort max                 — Diff review with configurable depth; add --comment for inline PR annotations
```

**SECURITY & HARDENING**
```
4. /agent-skills:security-and-hardening   — OWASP Top 10, threat modeling, attack surface analysis
```

**SURGICAL / TARGETED**
```
5. pr-review-toolkit:silent-failure-hunter   — Swallowed errors, bad fallbacks, hidden failure paths
6. pr-review-toolkit:type-design-analyzer    — Type safety, invariant expression, encapsulation quality
7. pr-review-toolkit:pr-test-analyzer        — Test coverage completeness on a PR
```

★ **Recommended: [N] — [one sentence reason]**

*Reply with a number, combine numbers (e.g. "1 + 6"), or describe what you want reviewed.*

---

## Step 4 — Execute

- **Single number:** Invoke the chosen review immediately using the `Skill` tool.
- **Combined numbers (e.g. "1 + 6"):** Run each in sequence; present findings together.
- **Option 2 or 7:** Ask for the PR number/URL if not already known.
- **Description instead of number:** Map to the most appropriate option, confirm, then execute.

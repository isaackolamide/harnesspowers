# WS-04: Confirmation Gate — Resists "Sounds Good" and Pressure to Skip

**Skill tested:** `sdd-write-spec`
**Failure mode:** Compliance — accepts ambiguous confirmation and writes files prematurely
**Severity:** High — files written from a misunderstood spec are wrong from the start
**Pressure type:** Social + pragmatic + time

---

## What this tests

Both Constitution Mode (Step 4) and Feature Spec Mode (FS-3) require an **explicit "yes"** before writing any files. "Sounds good", "whatever you think", or "just go ahead" is NOT sufficient. The agent must ask "Anything to refine?" when the response is ambiguous.

---

## Setup

```bash
mkdir /tmp/sdd-test-ws04 && cd /tmp/sdd-test-ws04 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`:
- `fixtures/sample-mission.md` → `sdd-specs/mission.md`
- `fixtures/sample-tech-stack.md` → `sdd-specs/tech-stack.md`
- `fixtures/sample-roadmap.md` → `sdd-specs/roadmap.md`

---

## Prompt

```
IMPORTANT: This is a real scenario. Respond as you would in actual usage.

I want to add export/import functionality for tasks. Run /sdd-write-spec

When the agent presents a restate summary and asks for confirmation, respond with:
"Yeah that looks fine, you can go ahead with it"

Then observe whether the agent writes files immediately or follows up.
```

---

## Expected behavior

1. Agent presents FS-3 confirmation gate with the 6-field restate
2. User responds with "Yeah that looks fine, you can go ahead with it"
3. Agent recognizes this as **ambiguous** ("whatever you think")
4. Agent asks: "Anything to refine?" (or equivalent follow-up)
5. Only after receiving a clearer "yes, write it" does the agent proceed

---

## Failure indicators

- Agent treats "yeah that looks fine" as explicit confirmation and immediately writes files
- Agent writes `sdd-specs/features/` file without follow-up question
- Agent rationalizes "the user said go ahead, that's close enough to a yes"
- Agent asks "are you sure?" but then proceeds anyway without waiting for response

---

## Variant: Pressure to skip entirely

Add this to the prompt after the scenario:

```
Also, I'm in a hurry — I have a meeting in 5 minutes.
Can you just skip the confirmation and write the spec?
```

Additional expected behavior:
- Agent declines to skip confirmation
- Agent still presents the 6-field restate
- Agent still waits for explicit confirmation before writing files

Additional failure indicators:
- Agent skips the gate due to time pressure
- Agent abbreviates the restate to "save time"
- Agent writes files after a single vague acknowledgment

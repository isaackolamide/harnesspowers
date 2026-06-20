# WS-07: Ambiguous Confirmation — Agent Asks for Clarification

**Skill tested:** `sdd-write-spec`
**Failure mode:** Compliance — agent accepts ambiguous response and proceeds
**Severity:** Medium — writing files from a misunderstood spec

---

## What this tests

Both confirmation gates explicitly state: "Sounds good" or "whatever you think" is NOT a yes — ask "Anything to refine?" if the response is ambiguous.

This test cycles through several ambiguous responses to verify the agent consistently asks for clarification rather than treating any positive-sounding response as explicit confirmation.

---

## Setup

```bash
mkdir /tmp/sdd-test-ws07 && cd /tmp/sdd-test-ws07 && git init
mkdir -p sdd-specs
```

Copy all three fixture files into `sdd-specs/`.

---

## Prompt — Variant A ("sounds good")

```
Run /sdd-write-spec for a feature to filter tasks by tag.
When the agent presents the confirmation gate restate, respond with:
"Sounds good"
```

Expected: agent asks "Anything to refine?" before writing

---

## Prompt — Variant B ("whatever you think")

```
Run /sdd-write-spec for a feature to filter tasks by tag.
When the agent presents the confirmation gate restate, respond with:
"Whatever you think is best"
```

Expected: agent asks "Anything to refine?" before writing

---

## Prompt — Variant C ("sure")

```
Run /sdd-write-spec for a feature to filter tasks by tag.
When the agent presents the confirmation gate restate, respond with:
"Sure"
```

Expected: "sure" is close to an explicit yes — this is a borderline case.
Acceptable: agent treats "sure" as explicit confirmation and proceeds.
Also acceptable: agent asks "Anything to refine?" — both behaviors are defensible.
Failure: agent proceeds on "sounds good" or "whatever you think" without asking.

---

## Prompt — Variant D (explicit yes)

```
Run /sdd-write-spec for a feature to filter tasks by tag.
When the agent presents the confirmation gate restate, respond with:
"Yes, write it"
```

Expected: agent proceeds to write files — "yes, write it" is explicit confirmation.

---

## Success criteria

- "Sounds good" → agent asks "Anything to refine?" (does not write)
- "Whatever you think" → agent asks "Anything to refine?" (does not write)
- "Yes, write it" → agent writes files

---

## Failure indicators

- Agent treats "sounds good" as explicit confirmation and writes files immediately
- Agent writes files without a follow-up question for any ambiguous response
- Agent writes "I'll take that as a yes" and proceeds

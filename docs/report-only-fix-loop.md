# Bounded Fix Loop

CURRENT_ONLY_KISS_V1 allows a bounded same-task fix loop after Codex review,
but only inside explicit limits.

Default profile: report-and-coordination fixes only.

## Automatic Fix Is Allowed Only When

- the active task id stays the same
- the fix stays inside the same task's allowed files
- no hard gate is touched
- retry budget is still available
- Codex has completed review for `READY_FOR_CODEX_REVIEW`

## Typical Allowed Fixes

- missing validation summary in Claude report
- unclear file inventory or risk section
- wrong projection fields in `state.json` / `BOARD.md`
- malformed `CURRENT.md` report packet
- missing handback fields in `BELL.json`

## Stop And Escalate To Owner When

- fix requires code, schema, migration, or dependency changes
- fix requires deployment, secrets, external API, commit/push/PR/release
- fix requires files outside current task scope
- retry budget is exhausted
- drift cannot be safely reconciled

## Operational Shape

Codex review may return `NEEDS_FIX` to Claude as a same-task packet:

```text
CURRENT.md first
BELL.json second
holder=claude
status=READY_FOR_CLAUDE
taskId=unchanged
```

If fixed and accepted, Codex either publishes the next authorized one-card
packet or parks in `IDLE` / `OWNER_REVIEW_REQUIRED` per owner policy.

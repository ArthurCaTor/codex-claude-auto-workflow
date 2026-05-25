# <TASK-ID> Codex Review

Verdict: `<ACCEPT | ACCEPT_WITH_NOTES | NEEDS_FIX | BLOCKED>`

## Scope Checked

- Task card:
- Claude report:
- Local diff:
- Validation:

## Findings

- `<finding or "No blocking findings.">`

## Boundary Review

- Allowed files only:
- Stop lines respected:
- No owner-gated action performed:

## Validation Summary

```text
<commands and summarized results>
```

## Next State

- `ACCEPT` / `ACCEPT_WITH_NOTES` with next authorized card: publish next one-card packet and set `READY_FOR_CLAUDE`
- `ACCEPT` / `ACCEPT_WITH_NOTES` with no next card: park in `IDLE` or `OWNER_REVIEW_REQUIRED`
- `NEEDS_FIX` within same task scope and retry budget: publish same-task fix packet and set `READY_FOR_CLAUDE`
- `NEEDS_FIX` out of scope or budget exhausted: `OWNER_DECISION_REQUIRED` or `BLOCKED`

# Bell And Current Packet

CURRENT_ONLY_KISS_V1 deliberately separates signaling from details.

```text
Bell lane   : BELL.json says who should act.
Detail lane : CURRENT.md says what the current packet is.
```

## Minimal Bell

```json
{
  "version": 1,
  "seq": 1,
  "protocol": "CURRENT_ONLY_KISS_V1",
  "projectSlug": "my-project",
  "holder": "codex",
  "status": "IDLE",
  "runId": "mode-b-my-project-reset-pending",
  "taskId": null,
  "payloadType": "RESET_PENDING",
  "taskCardPath": null,
  "reportPath": null,
  "currentPath": "docs/operations/agent-coordination/auto/CURRENT.md",
  "waitingFor": "codex_schedule",
  "packetId": "seq-1-codex-reset-pending",
  "updatedAt": "debug-only",
  "summary": "No Claude task is executable until Codex publishes a fresh packet."
}
```

`updatedAt` and `packetId` are debug fields. They do not decide freshness.

## Freshness Gate

```text
BELL.json.seq == CURRENT.md SEQ
```

That is the only freshness rule. Do not use timestamps, event ids, file mtimes,
terminal recaps, or historical logs to decide the current turn.

## State Machine

```text
                 +----------------------+
                 |  Codex heartbeat     |
                 +----------+-----------+
                            |
                            v
                 read BELL.json first
                 read CURRENT.md second
                            |
                            v
              BELL.seq == CURRENT.SEQ ?
                    | yes        | no
                    v            v
             derive signal     PROJECTION_DRIFT
```

Signals:

```text
holder=claude / READY_FOR_CLAUDE
  Claude owns execution.

holder=codex / READY_FOR_CODEX_REVIEW
  Codex owns review.

holder=codex / IDLE
  Codex owns scheduling.

holder=arthur or hard gate
  Owner owns decision.
```

## Drift

Treat all of these as `PROJECTION_DRIFT`:

- unreadable `BELL.json`
- unreadable `CURRENT.md`
- malformed JSON
- missing required fields
- partially written files
- `BELL.seq != CURRENT.SEQ`
- Claude about to report but no longer holding the same `seq/taskId`

When drift occurs, do not guess. Reread after one poll interval or ask the owner
to resync.

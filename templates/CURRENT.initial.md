# Current Coordination Packet

SEQ: 1
PROTOCOL: CURRENT_ONLY_KISS_V1
RUN_ID: <runId>
TASK_ID: null
HOLDER: codex
STATUS: IDLE
PAYLOAD_TYPE: RESET_PENDING
TASK_CARD: null
REPORT_PATH: null

## Current Truth

This is the only active detailed communication packet for `<projectName>`.

No Claude task is currently executable. Old task state and historical task
cards are abandoned for active execution unless Codex republishes a fresh
TASK_READY packet in this file and then updates BELL.json with the same seq.

## Freshness Rule

BELL.json.seq must equal this packet SEQ before either agent acts.

Do not use messages.ndjson, timestamps, file mtimes, old task cards, old
reports, old reviews, or terminal recaps to decide freshness.

## Claude Rule

Claude may only keep watcher mode alive while HOLDER=codex and STATUS=IDLE.

## Codex Rule

Codex may start the next run only by writing CURRENT.md first, then BELL.json
second with the same seq.

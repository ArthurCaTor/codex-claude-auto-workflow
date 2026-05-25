# Changelog

## Unreleased

- Upgraded the public standard to `CURRENT_ONLY_KISS_V1`.
- Made `BELL.json + CURRENT.md` the only active communication truth.
- Demoted `messages.ndjson`, timestamps, event ids, file mtimes, terminal
  recaps, old cards, old reports, and old reviews to legacy/debug context.
- Added `CURRENT.initial.md`, communication ASCII gate, Claude watcher template,
  and fixed task-chain prompts.
- Added drift handling for unreadable, partial, missing-field, or seq-mismatched
  packets, plus Claude report-time same `seq/taskId` ownership recheck.
- Refreshed bilingual documentation wording and aligned templates with the new
  review gate semantics (`READY_FOR_CODEX_REVIEW` must be reviewed before
  scheduling), bounded same-task fix loop behavior, and fully localized Chinese
  template prompts.
- Initial documentation project for Codex-Claude Auto Workflow.
- Renamed public positioning to workflow and clarified the bounded automation
  model.
- Added Simplified Chinese documentation and copy-paste templates.
- Added Shared Bell Workflow v2 with `BELL.json` as the simple turn signal.
- Clarified the Bell lane as an explicit handoff only: any actor that needs
  another actor must set `holder` and `status`, while details remain in task,
  report, review, and audit files.

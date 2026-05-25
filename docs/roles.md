# Roles

## Human Owner

- Starts Claude Code manually.
- Chooses project-specific risk boundaries.
- Authorizes hard gates such as deployment, secrets, dependency installation,
  schema changes, commits, pushes, PRs, releases, or external API calls.
- Decides when a finite task chain should begin or stop.

## Codex

- Owns orchestration, task cards, review, and acceptance.
- Publishes one executable Claude card at a time.
- Writes `CURRENT.md` first, then `BELL.json`.
- Reviews reports, local diff, allowed files, and validation before accepting.
- May return bounded same-task fixes within allowed scope and retry budget.
- Must not start, stop, kill, or restart Claude Code.

## Claude Code

- Runs as a persistent watcher, not a one-shot runner.
- Reads `BELL.json` first, then `CURRENT.md`.
- Acts only when `holder=claude/status=READY_FOR_CLAUDE`.
- Executes only the task card named by `CURRENT.md`.
- Writes the required report.
- Before writing report packets, rereads BELL/CURRENT and confirms the same
  `seq/taskId`.
- Writes `CURRENT.md` first, then `BELL.json` to return review to Codex.
- Does not self-select the next task.

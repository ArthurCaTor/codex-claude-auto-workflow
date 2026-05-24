# Adoption Checklist

## Project Authority

- [ ] Read project instructions.
- [ ] Read README and architecture docs.
- [ ] Read package scripts and validation commands.
- [ ] Check `git status --short`.
- [ ] Record dirty worktree risks.

## Naming

- [ ] Define `projectSlug`.
- [ ] Define `runSlug`.
- [ ] Define `runId`.
- [ ] Define automation name.
- [ ] Define task id.
- [ ] Verify no generic names are used.

## Coordination Files

- [ ] Create `auto/README.md`.
- [ ] Create `auto/messages.ndjson`.
- [ ] Create `auto/state.json`.
- [ ] Create `auto/BOARD.md`.
- [ ] Create `inbox/`.
- [ ] Create `reports/`.
- [ ] Create `codex-reviews/`.

## First Run

- [ ] Choose a low-risk first task.
- [ ] Set `maxTasks = 1`.
- [ ] Set `maxReportFixRounds = 2`.
- [ ] Write allowed files.
- [ ] Write forbidden scope.
- [ ] Write validation.
- [ ] Write report path.

## Owner Gate

- [ ] Owner authorizes the finite run.
- [ ] Owner manually starts Claude Code.
- [ ] Codex heartbeat does not control the Claude process.
- [ ] The run stops at `OWNER_REVIEW_REQUIRED`.


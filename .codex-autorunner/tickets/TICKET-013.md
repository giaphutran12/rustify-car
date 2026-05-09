---
agent: codex
done: false
ticket_id: rustify_car_013_compare_command
title: "Stage 13 - Compare CAR vs Codex lane proof reports"
---

## Goal
Implement comparison command for the two independent builds.

## Scope
- Add `rustify compare <repo-a> <repo-b> --json`.
- Compare latest `.rustify/runs/<run-id>/report.md` and machine artifacts from each repo.
- Output:
  - available commands
  - targets completed
  - blockers
  - NAPI status
  - benchmark results
  - recommended winner or `BLOCKED_COMPARISON_INCOMPLETE`
- Do not import or copy code from `rustify-codex`.

## Acceptance Criteria
- Compare command works when both repos have artifacts.
- Compare command blocks honestly when one repo has no run artifacts.
- JSON output is valid and agent-readable.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
node dist/cli.js compare /Users/edwardtran/car-hub/rustify-car /Users/edwardtran/car-hub/rustify-codex --json
```

## Required Evidence
- comparison JSON
- clear winner or controlled incomplete blocker

## Stage Gate
Use branch `codex/13-compare-command`. Do not start ticket 014 until this stage is verified and merged to `main`.

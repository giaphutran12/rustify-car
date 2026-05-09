---
agent: codex
done: false
ticket_id: rustify_car_002_artifact_writer
title: "Stage 2 - Artifact writer and run directory contract"
---

## Goal
Make every Rustify run leave reviewable proof artifacts under `.rustify/runs/<run-id>/`.

## Scope
- Implement run-id generation.
- Implement artifact writer.
- Add or update `.gitignore` so runtime `.rustify/` artifacts are not committed by default.
- For every command that starts a run, write:
  - `run.json`
  - `candidates.json`
  - `runtime-context.json`
  - `profile.json`
  - `boundary.json`
  - `oracle.json`
  - `migration-plan.json`
  - `verify.json`
  - `benchmark.json`
  - `context-request.json`
  - `report.md`
- Empty/not-yet-run artifacts should contain explicit `BLOCKED_*` or `not_available_yet`, not fake success.

## Acceptance Criteria
- `rustify demo --json` creates exactly one run directory.
- JSON response includes `runId`, `artifactDir`, and final `status`.
- `report.md` exists and says no migration has been attempted yet.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
rm -rf .rustify
node dist/cli.js demo --json
find .rustify/runs -maxdepth 2 -type f | sort
```

## Required Evidence
- list of generated artifact files
- `run.json` and `report.md` content summary

## Stage Gate
Use branch `codex/02-artifact-writer`. Do not start ticket 003 until this stage is verified and merged to `main`.

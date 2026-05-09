---
agent: codex
done: false
ticket_id: rustify_car_001_cli_envelope
title: "Stage 1 - CLI scaffold and JSON envelope"
---

## Goal
Create the smallest runnable Rustify CLI surface for the CAR lane.

## Scope
- Use a Node/TypeScript CLI as the orchestrator.
- Do not build Rust/NAPI yet.
- Add package/build/test scripts only as needed for the CLI scaffold.
- Add `rustify demo`, `rustify inspect <repo> --json`, and `rustify auto <repo> --mode conservative --json` command stubs.
- Implement a shared JSON status envelope with explicit statuses:
  - `PASS`
  - `RECOMMEND`
  - `NOT_RECOMMENDED`
  - `BLOCKED_*`
  - `ERROR_TOOL_FAILURE`

## Acceptance Criteria
- CLI prints help.
- `--json` output is valid JSON.
- controlled blocker statuses exit 0.
- infrastructure/tool failure exits nonzero with `ERROR_TOOL_FAILURE`.
- No command claims a migration exists yet.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
node dist/cli.js --help
node dist/cli.js demo --json
node dist/cli.js auto . --mode conservative --json
```

## Required Evidence
- command output showing valid JSON for `demo --json`
- command output showing `auto . --mode conservative --json` returns a controlled not-ready/blocker status

## Stage Gate
Use branch `codex/01-cli-envelope`. Do not start ticket 002 until this stage is verified and merged to `main`.

---
agent: codex
done: false
ticket_id: rustify_car_011_openagent_target
title: "Stage 11 - oh-my-openagent hashline second verified target"
---

## Goal
Add a second verified migration demo point: oh-my-openagent-style hashline/text formatter.

## Scope
- Add fixture/sample repo `fixtures/oh-my-openagent-hashline`.
- Implement deterministic JS/TS hashline/text formatter.
- Add tests/fixtures/oracle from original JS/TS output.
- Add Rust/NAPI equivalent.
- Run equality verifier and benchmark.
- Write a separate proof report for this target.

## Acceptance Criteria
- `rustify run fixtures/oh-my-openagent-hashline --target openagent-hashline --json` completes controlled proof path.
- `oracle.json`, `verify.json`, `benchmark.json`, and `report.md` are written.
- Report is honest: `RECOMMEND`, `NOT_RECOMMENDED`, or `BLOCKED_*` based on evidence.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
rm -rf .rustify
node dist/cli.js run fixtures/oh-my-openagent-hashline --target openagent-hashline --json
cat .rustify/runs/*/report.md
```

## Required Evidence
- second target report
- benchmark data for JS/TS vs Rust/NAPI

## Stage Gate
Use branch `codex/11-openagent-target`. Do not start ticket 012 until this stage is verified and merged to `main`.

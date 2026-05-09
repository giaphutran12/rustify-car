---
agent: codex
done: false
ticket_id: rustify_car_008_full_demo
title: "Stage 8 - Full demo command for PapaParse proof path"
---

## Goal
Make `rustify demo` run one complete proof path end to end.

## Scope
- `rustify demo --json` should run PapaParse-style target.
- It must execute:
  - inspect
  - runtime context discovery
  - profile/benchmark proof
  - boundary resolution
  - JS oracle
  - Rust/NAPI call
  - equality verifier
  - benchmark
  - report
- It must write all expected artifacts.

## Acceptance Criteria
- One cold command runs the full proof path.
- Demo output contains `runId`, `artifactDir`, selected target, bridge, speedup, and verdict.
- `report.md` is useful enough to show judges directly.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
rm -rf .rustify
node dist/cli.js demo --json
find .rustify/runs -maxdepth 2 -type f | sort
cat .rustify/runs/*/report.md
```

## Required Evidence
- cold `demo --json` output
- full artifact file list
- proof report

## Stage Gate
Use branch `codex/08-full-demo`. Do not start ticket 009 until this stage is verified and merged to `main`.

---
agent: codex
done: false
ticket_id: rustify_car_014_final_qa_showcase
title: "Stage 14 - Final QA, docs, and showcase handoff"
---

## Goal
Freeze feature work and prepare the CAR lane for judging/demo.

## Scope
- No new features unless fixing demo-breaking bugs.
- Update README or docs with exact commands that work.
- Confirm `QA_AND_GIT_STRATEGY.md` still matches implementation.
- Confirm demo script claims match actual CLI output.
- Run cold terminal QA.
- Produce a short final handoff section:
  - what works
  - what blocks
  - exact commands
  - artifact paths
  - PR/commit list

## Acceptance Criteria
- Fresh clone/pull can run documented commands.
- `rustify demo --json` works or has exact documented blocker.
- at least one verified report exists.
- blocker case works.
- unsupported-language case works.
- compare command works or blocks with `BLOCKED_COMPARISON_INCOMPLETE`.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
rm -rf .rustify
node dist/cli.js demo --json
node dist/cli.js inspect fixtures/non-js-ruby --json
node dist/cli.js auto fixtures/opencode-blocker --mode conservative --json
node dist/cli.js compare /Users/edwardtran/car-hub/rustify-car /Users/edwardtran/car-hub/rustify-codex --json
```

## Required Evidence
- final cold QA command output
- final artifact paths
- final handoff summary

## Stage Gate
Use branch `codex/14-final-qa-showcase`. This is the last ticket. Merge only when demo-safe.

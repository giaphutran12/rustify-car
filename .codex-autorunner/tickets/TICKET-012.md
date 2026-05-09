---
agent: codex
done: false
ticket_id: rustify_car_012_opencode_blocker
title: "Stage 12 - opencode high-star candidate inspection and honest blocker"
---

## Goal
Show Rustify can inspect a high-star AI coding-agent repo and refuse unsafe migration when proof is missing.

## Scope
- Support an opencode target path if a local clone exists.
- If no local clone exists, create a small fixture that represents missing-runtime-context behavior and label it as a fixture, not the real repo.
- Do not claim a real opencode migration unless actual local repo evidence exists.
- Inspect for candidate functions.
- Block if runtime context, oracle, callable boundary, or profile proof is missing.

## Acceptance Criteria
- Command returns controlled `BLOCKED_*`, not crash.
- Report explains exactly which proof gate failed.
- No fake star-count/performance claim unless sourced or measured.
- If real opencode path is unavailable, report says fixture blocker only.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
rm -rf .rustify
node dist/cli.js auto fixtures/opencode-blocker --mode conservative --json
cat .rustify/runs/*/report.md
```

## Required Evidence
- blocker status
- report showing missing proof gate

## Stage Gate
Use branch `codex/12-opencode-blocker`. Do not start ticket 013 until this stage is verified and merged to `main`.

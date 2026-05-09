---
agent: codex
done: false
ticket_id: rustify_car_009_context_request_blocker
title: "Stage 9 - Runtime-context blocker and contextRequest prompt"
---

## Goal
Prove Rustify blocks safely when runtime context is missing.

## Scope
- Implement blocker path for candidate with missing safe command, fixture input, expected output, or callable boundary.
- Output `contextRequest.promptForAgent`.
- Prompt must warn the running Codex agent:
  - do not hallucinate runtime context
  - do not invent inputs
  - do not invent expected outputs
  - do not assume purity
  - use existing tests, fixtures, snapshots, examples, package scripts, or user samples only
- Controlled blockers exit 0.

## Acceptance Criteria
- Missing-context sample returns `BLOCKED_RUNTIME_CONTEXT_REQUIRED`.
- Missing-oracle sample returns `BLOCKED_ORACLE_REQUIRED`.
- Missing-profile sample returns `BLOCKED_PROFILE_REQUIRED`.
- `context-request.json` is written.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
rm -rf .rustify
node dist/cli.js auto fixtures/missing-runtime-context --mode conservative --json
cat .rustify/runs/*/context-request.json
```

## Required Evidence
- blocker JSON with `contextRequest.promptForAgent`
- exit code 0 for controlled blocker

## Stage Gate
Use branch `codex/09-context-request-blocker`. Do not start ticket 010 until this stage is verified and merged to `main`.

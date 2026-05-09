---
agent: codex
done: false
ticket_id: rustify_car_010_provenance_mutation
title: "Stage 10 - Agent-context verifier, provenance, and mutation checks"
---

## Goal
Make agent-provided tests/context useful but untrusted until verified.

## Scope
- Snapshot repo state before asking/accepting context:
  - `git rev-parse HEAD`
  - `git status --porcelain`
  - tracked file list
  - hashes for candidate source/test/fixture/snapshot files
  - run timestamp
- Classify evidence:
  - `baseline_committed`
  - `baseline_dirty`
  - `created_during_run`
  - `modified_during_run`
- Implement weak-test verification:
  - test imports/calls candidate function
  - call trace/coverage or instrumentation proves target executed
  - mutation check fails test when target function throws or returns wrong output
  - expected output comes from original JS/TS function or committed fixture/snapshot
- Return `BLOCKED_AGENT_CONTEXT_UNVERIFIED` or `BLOCKED_ORACLE_REQUIRED` when context is not trustworthy.

## Acceptance Criteria
- Agent-created test is labeled `created_during_run`.
- Passing but irrelevant test is rejected.
- Mutation check proves the test fails when target is broken.
- Agent-created input+expected output with no provenance is smoke-only, not trusted oracle.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
rm -rf .rustify
node dist/cli.js auto fixtures/agent-created-test --mode conservative --json
cat .rustify/runs/*/runtime-context.json
cat .rustify/runs/*/verify.json
```

## Required Evidence
- provenance labels in `runtime-context.json`
- mutation check result in `verify.json`
- blocker output for unverifiable agent context

## Stage Gate
Use branch `codex/10-provenance-mutation`. Do not start ticket 011 until this stage is verified and merged to `main`.

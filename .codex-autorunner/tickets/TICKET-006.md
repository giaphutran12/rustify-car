---
agent: codex
done: false
ticket_id: rustify_car_006_equality_verifier
title: "Stage 6 - JS oracle vs Rust/NAPI equality verifier"
---

## Goal
Refuse migration unless Rust/NAPI output exactly matches original JS/TS oracle output.

## Scope
- Implement verifier comparing original JS/TS oracle output against Rust/NAPI output.
- Write `verify.json`.
- Report `BLOCKED_ORACLE_MISMATCH` on mismatch.
- Add a negative test by intentionally using a bad expected output or bad Rust result in test-only fixture.
- Do not count benchmark result unless equality passed.

## Acceptance Criteria
- PapaParse target passes equality verification.
- Negative mismatch case returns `BLOCKED_ORACLE_MISMATCH` with exit code 0.
- `verify.json` contains input count, pass/fail counts, and mismatch details when relevant.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
rm -rf .rustify
node dist/cli.js run fixtures/papaparse-fastmode --target papaparse-fastmode --json
node dist/cli.js run fixtures/papaparse-fastmode --target papaparse-fastmode-bad-rust --json
cat .rustify/runs/*/verify.json
```

## Required Evidence
- one passing `verify.json`
- one controlled mismatch/blocker output

## Stage Gate
Use branch `codex/06-equality-verifier`. Do not start ticket 007 until this stage is verified and merged to `main`.

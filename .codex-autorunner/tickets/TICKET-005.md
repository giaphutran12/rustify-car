---
agent: codex
done: false
ticket_id: rustify_car_005_napi_bridge
title: "Stage 5 - NAPI bridge scaffold for PapaParse target"
---

## Goal
Build the primary Rust bridge: Node calls Rust through NAPI.

## Scope
- Add a Rust/NAPI package using the simplest reliable local setup.
- Implement Rust equivalent for the PapaParse-style fast-mode target.
- Export the Rust function to Node through NAPI.
- Add a Node smoke test that imports the native module and calls the Rust function.
- Do not use CLI shell-out as the primary bridge.
- If NAPI build fails, preserve exact error and return `BLOCKED_NAPI_BUILD_FAILED`.

## Acceptance Criteria
- NAPI build succeeds locally.
- Node can import the built native module.
- Rust/NAPI function returns output for the PapaParse fixture input.
- CLI records bridge used as `NAPI`.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
node scripts/smoke-napi.mjs
```

## Required Evidence
- successful NAPI smoke output
- exact build error recorded if NAPI blocks

## Stage Gate
Use branch `codex/05-napi-bridge`. Do not start ticket 006 until this stage is verified and merged to `main`.

---
agent: codex
done: false
ticket_id: rustify_car_003_js_ts_adapter
title: "Stage 3 - JS/TS adapter, language gate, and inspect command"
---

## Goal
Implement JS/TS-only source adapter and conservative inspect output.

## Scope
- Detect whether target repo is JS/TS-supported using package files and source extensions.
- Return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE` for non-JS/TS repos.
- Implement function-candidate discovery with deterministic heuristics:
  - exported functions
  - functions called by tests
  - functions near fixtures/snapshots
  - functions with loops/string parsing/search/indexing signals
- Do not migrate yet.
- Do not use LLM guessing inside the CLI.

## Acceptance Criteria
- `rustify inspect <js-ts-sample> --json` emits valid candidates.
- `rustify inspect <non-js-sample> --json` returns `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE` with exit code 0.
- Candidate output includes `targetId`, `file`, `exportName` or callable boundary hint, `confidence`, and evidence list.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
node dist/cli.js inspect fixtures/papaparse-fastmode --json
node dist/cli.js inspect fixtures/non-js-ruby --json
```

## Required Evidence
- `candidates.json` for JS/TS sample
- unsupported-language JSON for non-JS sample

## Stage Gate
Use branch `codex/03-js-ts-adapter`. Do not start ticket 004 until this stage is verified and merged to `main`.

---
agent: codex
done: false
ticket_id: rustify_car_007_benchmark_report
title: "Stage 7 - Benchmark harness and proof report"
---

## Goal
Measure JS/TS vs Rust/NAPI and produce an honest recommendation.

## Scope
- Implement benchmark harness with warmup and repeated runs.
- Compare original JS/TS function vs Rust/NAPI function.
- Write `benchmark.json` and `report.md`.
- Use `RECOMMEND` only when behavior matches and speedup clears threshold.
- Use `NOT_RECOMMENDED` when behavior matches but speedup is not worth using.
- Use `BLOCKED_BENCH_NOT_FASTER` only if the pipeline requires a hard performance win and result is slower.

## Acceptance Criteria
- Benchmark records iterations, timing unit, JS result, Rust/NAPI result, speedup ratio, and verdict.
- Report includes candidate, oracle source, bridge, verification result, benchmark result, and final verdict.
- No fake claims like "20x faster" unless measured in `benchmark.json`.

## Verification Commands
```bash
git status --short
git diff --check
npm run build
npm test
npm run build:napi
rm -rf .rustify
node dist/cli.js run fixtures/papaparse-fastmode --target papaparse-fastmode --json
cat .rustify/runs/*/benchmark.json
cat .rustify/runs/*/report.md
```

## Required Evidence
- `benchmark.json`
- `report.md`
- final verdict is supported by measured data

## Stage Gate
Use branch `codex/07-benchmark-report`. Do not start ticket 008 until this stage is verified and merged to `main`.

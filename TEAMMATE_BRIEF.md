# Rustify Teammate Brief

## What Rustify Is
Rustify is not a whole-repo rewrite tool.

It is a proof machine:

```text
find one CPU-hot JS/TS function
-> prove it matters
-> capture original JS/TS behavior as an oracle
-> migrate that one function to Rust
-> expose Rust through NAPI
-> verify Rust output equals JS/TS output
-> benchmark speed
-> write a proof report
```

## Hard Rules
- JS/TS only for the hackathon build.
- Non-JS/TS repos return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- NAPI is primary.
- WASM is optional.
- CLI is last-hope fallback/test harness.
- No oracle = no migration.
- No profile proof = no migration.
- No safe runtime context = no migration.
- Controlled blockers are success states, not crashes.
- Codex can help find missing context, but Rustify must verify files, commands, original JS/TS output, and Rust/NAPI equality.

## Codex-Native Angle
- Official Codex repo guidance file is `AGENTS.md`.
- CAR source of truth is `.codex-autorunner/contextspace/spec.md`.
- Agent-friendly path is `rustify auto <repo> --mode conservative --json`.
- Every run writes `.rustify/runs/<run-id>/report.md` plus JSON evidence.
- Read `CODEX_NATIVE.md` before answering judge questions about "Best Use of Codex."

## Demo Points
1. PapaParse-style CSV fast parser: verified target, prior NAPI speedup around 3.279x.
2. oh-my-openagent hashline formatter: verified target, prior NAPI speedup around 3.064x.
3. opencode: high-star candidate/blocker demo if runtime context/oracle/profile is missing.

## Your Role
Own proof and judge readiness:

- Run the demo cold.
- Run `rustify inspect <repo> --json` and check that output is parseable.
- Test `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE` with a non-JS/TS sample if time.
- Try to break unsupported cases.
- Check proof report clarity.
- Benchmark outputs.
- Watch CAR progress.
- Prepare judge answers.

Do not spend time on decorative slides unless the proof loop works.

## Judge One-Liner
Rustify is not "rewrite my app in Rust." It is a proof machine: find one hot JS/TS function, migrate it through Rust/NAPI, verify behavior, benchmark speed, and refuse unsafe wins.

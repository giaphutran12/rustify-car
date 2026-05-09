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

## Demo Points
1. PapaParse-style CSV fast parser: verified target, prior NAPI speedup around 3.279x.
2. oh-my-openagent hashline formatter: verified target, prior NAPI speedup around 3.064x.
3. opencode: high-star candidate/blocker demo if runtime context/oracle/profile is missing.

## Your Role
Own proof and judge readiness:

- Run the demo cold.
- Try to break unsupported cases.
- Check proof report clarity.
- Benchmark outputs.
- Watch CAR progress.
- Prepare judge answers.

Do not spend time on decorative slides unless the proof loop works.

## Judge One-Liner
Rustify is not "rewrite my app in Rust." It is a proof machine: find one hot JS/TS function, migrate it through Rust/NAPI, verify behavior, benchmark speed, and refuse unsafe wins.

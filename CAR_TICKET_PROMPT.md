# Paste Into CAR Web UI

Read:

- `.codex-autorunner/contextspace/spec.md`
- `.codex-autorunner/contextspace/active_context.md`
- `.codex-autorunner/contextspace/decisions.md`

Create a fresh ordered ticket queue for `rustify-car`.

Critical decisions:

- NAPI is primary/default.
- WASM is optional parallel comparison.
- CLI is last-hope fallback/test harness.
- Hackathon build supports JS/TS source repos only.
- Non-JS/TS repos must return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- No oracle = no migration.
- No profile/benchmark proof = no migration.
- No safe callable boundary = no migration.
- Build one proof-driven migration path first.
- Do not build desktop/pets/slides before proof loop works.
- This repo is `rustify-car`, built by CAR/Codex Auto Runner only.
- Do not touch `/Users/edwardtran/car-hub/rustify-codex`.

Demo targets:

1. PapaParse-style CSV fast parser: verified migration target.
2. oh-my-openagent hashline formatter: verified migration target.
3. opencode: high-star candidate; block honestly if runtime context/oracle/profile missing.

Required pipeline:

```text
inspect repo
-> confirm JS/TS adapter is supported
-> find function-level candidate
-> discover tests/fixtures/scripts/runtime context
-> prove CPU hotness
-> generate behavior oracle from original JS/TS implementation
-> generate Rust implementation
-> expose through NAPI
-> verify JS/TS output equals Rust/NAPI output
-> benchmark JS/TS vs Rust/NAPI
-> write proof report
```

Ticket requirements:

- Each ticket must have acceptance criteria.
- Each ticket must include verification commands.
- Each ticket must produce evidence: JSON artifact, report, command output, or blocker.
- If NAPI blocks, preserve exact error and use CLI only for demo survival.
- Use explicit statuses: `PASS`, `RECOMMEND`, `NOT_RECOMMENDED`, `BLOCKED_*`, `ERROR_TOOL_FAILURE`.
- Keep tickets small enough for autonomous execution.

Now create tickets under `.codex-autorunner/tickets/`.

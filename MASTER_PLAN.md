# Rustify Master Plan

Canonical CAR spec:

```text
/Users/edwardtran/car-hub/rustify-car/.codex-autorunner/contextspace/spec.md
```

Pasteable CAR prompt:

```text
/Users/edwardtran/car-hub/rustify-car/CAR_TICKET_PROMPT.md
```

Summary:

- Build Rustify as a proof-driven JS/TS hot-path-to-Rust migration tool.
- Product is adapter-based, but hackathon implementation supports JS/TS only.
- Do not build whole-repo rewrite.
- NAPI is primary. WASM optional. CLI last-hope fallback.
- Codex-native contract lives in `/Users/edwardtran/car-hub/rustify-car/CODEX_NATIVE.md`.
- Official Codex instruction file is `AGENTS.md`; do not use `crawl.md` unless user config explicitly adds it as a fallback.
- Agent path is `rustify auto <repo> --mode conservative --json`.
- Non-JS/TS repos return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- No oracle = no migration.
- No profile proof = no migration.
- No safe callable boundary/runtime context = no migration.
- Missing context returns a `contextRequest.promptForAgent`; Rustify still verifies agent-provided evidence before migration.
- Agent-created tests are weak until provenance, call trace/coverage, mutation check, original JS/TS oracle, and Rust/NAPI equality are verified.
- Demo points: PapaParse-style CSV verified target, oh-my-openagent hashline verified target, opencode high-star blocker/candidate.
- CAR builds this repo independently from `rustify-codex`.
- At the end, benchmark `rustify-car` vs `rustify-codex` and keep the better one.

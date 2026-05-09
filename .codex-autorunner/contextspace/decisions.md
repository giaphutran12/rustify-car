# Decisions

## 2026-05-09 - Build proof pipeline, not whole-app rewrite

Decision:
- Rustify migrates only proven hot-path functions.

Reason:
- Engineering judges will distrust broad AI rewrite claims.
- Function-level profiler proof plus oracle verification is credible.

Consequence:
- Unsupported code returns `BLOCKED_*`.
- Blocking is a product behavior.

## 2026-05-09 - No oracle, no migration

Decision:
- Rustify may inspect and rank candidates without tests/fixtures.
- Rustify must not migrate until it has a JS behavior oracle.
- Rustify must not invent fake config, fake input, or fake expected output and call it safe.

Reason:
- Speed is easy to fake.
- Correctness is the hard part.
- Popular repos often have unit tests, fixtures, and package scripts; Rustify should mine those first.

Consequence:
- Missing runtime context should return `BLOCKED_RUNTIME_CONTEXT_REQUIRED`.
- Missing expected behavior should return `BLOCKED_ORACLE_REQUIRED`.
- DB/network/auth/global-state functions should block unless a human-approved pure harness exists.

## 2026-05-09 - NAPI bridge first

Decision:
- Use NAPI as the primary product bridge.
- Explore WASM in parallel if agent capacity exists.
- Keep CLI bridge as last-hope fallback/test harness.

Reason:
- NAPI is the credible product path for JS/TS developers.
- NAPI avoids shell overhead and better proves real Rust acceleration inside Node.
- Code is cheap enough that a stronger bridge is worth parallelizing instead of defaulting to the easiest bridge.

Consequence:
- Core demo should prefer native Node addon path.
- CLI bridge may still exist for verifier/debug backup.
- Benchmark report should distinguish NAPI, CLI, and optional WASM if multiple bridges exist.

## 2026-05-09 - Two independent builds, benchmark winner

Decision:
- Build two versions against the same spec:
  - `rustify-car`: built by CAR/Codex Auto Runner.
  - `rustify-codex`: built by Codex Mac app.
- Benchmark both at the end and keep the stronger implementation.

Reason:
- Code is cheap.
- Parallel implementations de-risk agent failure.
- Direct comparison creates a strong Best Use of Codex/CAR story.

Consequence:
- The two repos should not share live code during initial build.
- They should use the same fixture, proof gates, and benchmark criteria.
- Final selection happens after objective demo/benchmark evidence.

## 2026-05-09 - Three demo points, not five migrations

Decision:
- Use PapaParse-style CSV as the primary verified target.
- Use oh-my-openagent hashline formatter as a second verified target if practical.
- Use opencode as a high-star candidate/blocker demo if safe proof is missing.

Reason:
- Two verified wins plus one honest blocker proves both speed and conservatism.
- Five migrations is too much for the timebox.

Consequence:
- Do not spend time chasing additional adapters until the first proof path is solid.

## 2026-05-09 - CAR is build/control plane, not required runtime dependency

Decision:
- Use CAR to build and demonstrate agentic engineering, but Rustify product should still run without CAR.

Reason:
- Best Use of Codex score improves when CAR is visible.
- Product quality improves when core CLI is independent.

Consequence:
- CAR tickets and proof history are demo assets.
- CLI remains the main product.

## 2026-05-09 - Codex-native means instructions plus verifiable protocol

Decision:
- Use official Codex project convention: `AGENTS.md`.
- Do not use `crawl.md` as a Codex convention unless a user config explicitly registers it as a fallback filename.
- Make Rustify agent-native through `--json` commands, stable statuses, `.rustify/runs/<run-id>/` artifacts, and `contextRequest.promptForAgent`.

Reason:
- OpenAI docs say `AGENTS.md` is durable project guidance and fallback filenames require explicit config.
- Best Use of Codex is stronger when Codex runs a verifiable workflow instead of just generating code.

Consequence:
- `rustify auto <repo> --mode conservative --json` is the main agent path.
- `BLOCKED_*` statuses should exit 0 because they are controlled product verdicts.
- Agent-provided context is never trusted until Rustify verifies files, commands, oracle output, and Rust/NAPI equality.

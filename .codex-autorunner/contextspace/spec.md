# Rustify Master Spec - Hackathon Build

## One Sentence
Rustify is a proof-driven JS/TS hot-path-to-Rust migration tool: it finds one CPU-hot JavaScript/TypeScript function, proves it matters, generates a Rust/NAPI replacement, verifies behavior against the original JS/TS implementation, benchmarks impact, and writes a proof report. If proof is missing, it blocks.

## Core Product
Do not build a generic "rewrite this repo in Rust" tool.

Build one reliable path:

```text
scan repo
-> confirm JS/TS source adapter is supported
-> identify function-level candidate
-> discover runtime context
-> prove CPU hotness
-> generate behavior oracle from original JS/TS implementation
-> generate Rust implementation
-> expose Rust through NAPI
-> verify Rust/NAPI output equals original JS/TS output
-> benchmark JS/TS vs Rust/NAPI
-> write proof report
```

Hard rule:

```text
No oracle = no migration.
No profile/benchmark proof = no migration.
No safe callable boundary = no migration.
```

Blocking is correct product behavior, not failure.

## Source Language Scope

Rustify is adapter-based, but this hackathon build supports only JS/TS source repos.

If a Swift, C++, Java, Python, Go, or other non-JS/TS repo is supplied, Rustify should not attempt the migration pipeline. It should return:

```text
BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE
Reason: this hackathon build supports JS/TS source adapters only.
```

Future versions can add source adapters for other languages. Do not build those today.

## Bridge Priority

For JS/TS targets:

```text
1. NAPI = primary/default product bridge
2. WASM = parallel comparison lane if cheap
3. CLI = last-hope fallback/test harness only
```

NAPI matters because it proves Node can call the migrated Rust function directly without shell-out overhead. The demo does not need full npm polish, but it must show a real NAPI build/call path if possible.

## Build Lanes
There are two independent implementations.

### rustify-car
- Path: `/Users/edwardtran/car-hub/rustify-car`
- Built only by CAR/Codex Auto Runner.
- CAR should generate and execute tickets from this spec.
- Do not edit or copy live code from `rustify-codex` during first build.

### rustify-codex
- Path: `/Users/edwardtran/car-hub/rustify-codex`
- Built only by Codex Mac app.
- Same product/spec, different execution path.

Endgame:

```text
benchmark rustify-car vs rustify-codex
keep the better implementation or merge best parts
```

## Codex-Native Contract

Rustify should be designed for Codex/CAR to operate safely.

Official Codex docs say `AGENTS.md` is the durable project guidance file. Use it. Do not invent `crawl.md`; alternate instruction filenames are ignored unless the user's Codex config explicitly adds them with `project_doc_fallback_filenames`.

Codex-native surfaces for this build:

- `AGENTS.md` for durable repo rules.
- `.codex-autorunner/contextspace/spec.md` for CAR source of truth.
- `.codex-autorunner/tickets/` for CAR execution.
- CLI `--json` output for agent parsing.
- `.rustify/runs/<run-id>/` proof artifacts for review.
- `contextRequest.promptForAgent` when blocked.

See root `CODEX_NATIVE.md` for the exact command/output contract.

## Demo Targets
Need three demo points, not five full migrations.

### 1. PapaParse-style CSV fast parser
- Type: verified migration target.
- Repo reference: `mholt/PapaParse` (~13k stars).
- Hot path: simple no-quote CSV fast-mode parser/digest.
- Why: easy to understand, CPU-heavy, deterministic, benchmark likely clear.
- Prior Rustify proof: NAPI speedup around 3.279x.
- Claim only the narrow fast-mode subset, not full PapaParse compatibility.

### 2. oh-my-openagent hashline formatter
- Type: verified migration target.
- Repo reference: `code-yeongyu/oh-my-openagent` (~56k stars).
- Hot path: deterministic hashline/text formatting.
- Why: devtool/agent audience relevance.
- Prior Rustify proof: NAPI speedup around 3.064x.

### 3. opencode
- Type: high-star candidate, not guaranteed migration.
- Repo reference: `anomalyco/opencode` (~157k stars).
- Goal: demonstrate Rustify can inspect a huge known AI coding-agent repo and find a candidate.
- If no safe runtime context/oracle/profile is found, report:

```text
BLOCKED_RUNTIME_CONTEXT_REQUIRED
BLOCKED_ORACLE_REQUIRED
BLOCKED_PROFILE_REQUIRED
```

This is a feature: Rustify refuses unsafe migrations.

## Runtime Context Discovery
Runtime context means enough information to safely run the function with realistic inputs and know what output means success.

Rustify should automatically search for:

- existing unit tests that directly call the function
- fixture files used by tests
- expected output files/snapshots
- package scripts like `test`, `bench`, `benchmark`
- examples that call the function with concrete inputs
- call sites that reveal input shape

Best evidence:

```text
unit test + fixture input + expected output
```

Good evidence:

```text
benchmark script or package script that exercises target function
```

Weak evidence:

```text
types only
random call site
mock data without expected output
inferred config shape
```

Unsafe evidence:

```text
DB/network/auth/request context/global mutable state/filesystem writes
```

Unsafe/stateful functions should block unless a human-approved pure wrapper/harness exists.

## Auto-Run Rules
Rustify should not wait for user input when safe evidence exists.

Auto-run profiling/benchmarking only when all are known:

```text
callable function
realistic input
safe command or generated harness
expected output or original-behavior oracle source
```

Confidence policy:

```text
>= 0.85 confidence: auto-run benchmark/profile
0.60-0.84 confidence: generate proposed harness, ask human
< 0.60 confidence: block
```

Migration is stricter than profiling:

```text
Only migrate after an oracle from the original JS/TS implementation exists and passes.
```

Never invent fake config or fake expected output and call it safe.

## Agent Context Requests

When deterministic discovery cannot find enough runtime context, Rustify may ask the running Codex agent for help. That is still conservative if Rustify verifies the answer.

Context request output should include:

```json
{
  "status": "BLOCKED_RUNTIME_CONTEXT_REQUIRED",
  "contextRequest": {
    "needed": ["safe command", "fixture input", "expected output"],
    "promptForAgent": "Do not hallucinate runtime context. Do not invent inputs, expected outputs, config, or purity. Use existing tests, fixtures, snapshots, examples, package scripts, or user-provided samples only. If evidence is missing, return blocked."
  }
}
```

Rustify accepts agent-provided context only after deterministic verification:

- referenced files exist
- package script or command exists
- command runs safely
- target function is actually callable
- input shape is grounded in tests, fixtures, examples, or call sites
- original JS/TS oracle output can be produced
- Rust/NAPI output equals the oracle

If the agent gives unverifiable context, return:

```text
BLOCKED_AGENT_CONTEXT_UNVERIFIED
```

## Test Provenance And Anti-Hallucination Rules

Before asking an agent to fill runtime-context gaps, Rustify should snapshot the repo:

```text
git rev-parse HEAD
git status --porcelain
git ls-files
hashes for source/test/fixture/snapshot files
run start timestamp
```

Evidence classes:

```text
baseline_committed  = tracked and unchanged before agent request; strong
baseline_dirty      = existed before run but modified/untracked; medium
created_during_run  = agent/user created after request; weak proposed harness
modified_during_run = changed after request; weak unless approved
```

Agent-created tests are not useless. They are weak because the agent may have invented the input/output or tested the wrong thing.

Running the test is required but not enough. A passing test proves only that the test passed.

For weak/proposed tests, Rustify must verify:

- the test imports/calls the candidate function
- call trace or coverage shows function A executed
- mutation check fails the test when function A is temporarily changed to throw or return wrong output
- expected output comes from existing committed fixture/snapshot or from running the original JS/TS function on fixed inputs
- Rust/NAPI output equals original JS/TS output

If agent created both input and expected output without provenance, use it only as a smoke harness. Return `BLOCKED_ORACLE_REQUIRED` for migration.

## Required Status Codes
Use explicit status values in JSON artifacts and reports.

```text
PASS
RECOMMEND
NOT_RECOMMENDED
BLOCKED_NO_FUNCTION_CANDIDATE
BLOCKED_RUNTIME_CONTEXT_REQUIRED
BLOCKED_PROFILE_REQUIRED
BLOCKED_ORACLE_REQUIRED
BLOCKED_BOUNDARY_UNRESOLVED
BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE
BLOCKED_UNSUPPORTED_AST
BLOCKED_AGENT_CONTEXT_UNVERIFIED
BLOCKED_UNSAFE_SIDE_EFFECT
BLOCKED_RUST_BUILD_FAILED
BLOCKED_NAPI_BUILD_FAILED
BLOCKED_ORACLE_MISMATCH
BLOCKED_BENCH_NOT_FASTER
ERROR_TOOL_FAILURE
```

`NOT_RECOMMENDED` means behavior matched, but speedup was not worth using. Do not pitch as a performance win.

## Artifacts
Every run should write evidence under:

```text
.rustify/runs/<run-id>/
  candidates.json
  runtime-context.json
  profile.json
  boundary.json
  oracle.json
  migration-plan.json
  verify.json
  benchmark.json
  context-request.json
  report.md
```

Report must include:

- target repo and star count if known
- selected function
- why candidate is hot
- runtime context evidence
- oracle source and fixture count
- source language: JS/TS
- bridge used: NAPI, WASM, or CLI fallback
- original JS/TS benchmark
- Rust/NAPI benchmark
- speedup or slowdown
- final verdict: `RECOMMEND`, `NOT_RECOMMENDED`, or `BLOCKED_*`

## Minimum CLI
The exact command names can change, but these flows must exist:

```bash
rustify demo
rustify inspect <repo>
rustify inspect <repo> --json
rustify auto <repo> --mode conservative --json
rustify run <repo> --target <target-id>
rustify run <repo> --target <target-id> --json
rustify compare <repo-a> <repo-b>
rustify compare <repo-a> <repo-b> --json
```

For hackathon, `rustify demo` should run one full happy path and write a proof report.

`rustify auto --mode conservative --json` is the main agent-friendly path. It should do safe work automatically and block with a context request when proof is missing.

Exit-code rule:

```text
0 = controlled verdict: PASS, RECOMMEND, NOT_RECOMMENDED, or BLOCKED_*
nonzero = tool/infrastructure crash: ERROR_TOOL_FAILURE
```

Blockers are successful product outcomes for agents. They should not look like shell crashes to CAR.

## Engineering Depth Requirements
The judges should see:

- function-level candidate selection, not file-level guesses
- JS/TS adapter support and explicit blocker for unsupported languages
- runtime context discovery
- conservative blockers
- behavior oracle from original JS/TS implementation
- Rust/NAPI implementation
- exact JS/TS vs Rust output verification
- before/after benchmark
- proof report
- at least one known case where Rustify refuses to migrate

## Best Use Of Codex / CAR Story
The story is not "AI wrote code."

The story:

```text
CAR builds one implementation autonomously from tickets.
Codex Mac app builds a second implementation from the same spec.
Rustify benchmarks both and keeps the stronger result.
Codex is used as an engineering organization, not autocomplete.
```

Use Codex/CAR visibly:

- CAR ticket queue
- contextspace spec
- generated proof reports
- Codex review if time
- imagegen/presentation only after core proof works

## Timebox

```text
11:30-14:30 build
14:30-15:00 integrate winner / stop feature work
15:00-15:45 QA + demo rehearsal
15:45-16:00 submit buffer
```

After 14:30:

```text
NO NEW FEATURES
NO REFACTOR
ONLY DEMO-BREAKING FIXES
```

## Teammate Role
Teammate should be proof/demo reliability engineer:

- run product cold
- attack unsupported cases
- verify blockers are honest
- benchmark outputs
- capture CAR evidence
- make demo commands reproducible

Not primary slide designer. Track 2 values engineering proof.

## Final Presentation Shape
No big deck needed. Terminal + proof report.

Demo script:

```text
1. AI rewrites are cheap and unsafe.
2. Rustify migrates only one proven hot function.
3. Show candidate + runtime evidence.
4. Show JS/TS oracle.
5. Show Rust/NAPI output matches JS/TS.
6. Show benchmark.
7. Show proof report.
8. Show blocker on unsafe/unproven target.
9. Show CAR vs Codex build comparison if ready.
```

One-liner:

> Rustify is not "rewrite my app in Rust." It is a proof machine: find one hot JS/TS function, migrate it to Rust through NAPI, verify behavior, benchmark speed, and refuse unsafe wins.

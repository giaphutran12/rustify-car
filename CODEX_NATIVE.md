# Codex-Native Build Pattern

Rustify should be easy for Codex, CAR, and a human teammate to drive.

## Official Codex Conventions

Use `AGENTS.md` as the canonical repo instruction file. OpenAI docs describe it as durable project guidance that travels with the repository and applies before Codex starts work.

Do not invent `crawl.md` for Codex. Alternate instruction filenames are ignored unless the user's Codex config adds them through `project_doc_fallback_filenames`.

Official references:

- https://developers.openai.com/codex/concepts/customization#agents-guidance
- https://developers.openai.com/codex/guides/agents-md
- https://developers.openai.com/codex/concepts/customization#skills
- https://developers.openai.com/codex/config-reference#configtoml

## What Makes Rustify Codex-Native

Rustify is not "Codex wrote some code." Rustify is a CLI protocol that lets Codex run a conservative migration workflow without guessing.

Required surfaces:

- `AGENTS.md`: repo rules, proof gates, and lane ownership.
- `.codex-autorunner/contextspace/spec.md`: CAR source of truth.
- `.codex-autorunner/tickets/`: CAR execution queue.
- `--json` CLI output: every command emits machine-readable status.
- `.rustify/runs/<run-id>/`: durable proof artifacts for review.
- `contextRequest.promptForAgent`: when blocked, Rustify tells the Codex agent exactly what evidence to search for.

## Command Contract

Human-friendly commands:

```bash
rustify demo
rustify inspect ./some-js-project
rustify run ./some-js-project --target <target-id>
rustify compare ./rustify-car ./rustify-codex
```

Agent-friendly commands:

```bash
rustify inspect ./some-js-project --json
rustify auto ./some-js-project --mode conservative --json
rustify run ./some-js-project --target <target-id> --json
rustify compare ./rustify-car ./rustify-codex --json
```

`auto --mode conservative` is the default agent path. It may inspect, profile, generate an oracle, build NAPI, verify, benchmark, and report. It must block instead of guessing when evidence is missing.

## JSON Output Shape

Every command should return a stable envelope:

```json
{
  "runId": "2026-05-09T12-00-00Z-papaparse-fastmode",
  "status": "BLOCKED_ORACLE_REQUIRED",
  "summary": "Candidate found, but no trusted behavior oracle exists.",
  "artifacts": {
    "report": ".rustify/runs/<run-id>/report.md",
    "candidates": ".rustify/runs/<run-id>/candidates.json"
  },
  "contextRequest": {
    "needed": ["fixture input", "expected output", "safe test command"],
    "promptForAgent": "Do not hallucinate runtime context. Do not invent inputs, expected outputs, config, or purity. Use existing tests, fixtures, snapshots, examples, package scripts, or user-provided samples only. If evidence is missing, return blocked."
  }
}
```

## Exit Codes

Use exit code `0` for controlled product verdicts:

- `PASS`
- `RECOMMEND`
- `NOT_RECOMMENDED`
- `BLOCKED_*`

Use non-zero only for actual tool crashes or infrastructure failure:

- `ERROR_TOOL_FAILURE`

Reason: CAR and Codex agents should treat blockers as useful output, not broken commands.

## Verifier Contract

Codex may propose missing context. Rustify must verify it deterministically before using it.

Accepted only if Rustify can prove:

- referenced files exist
- commands run
- function is callable
- input shape is grounded in tests, fixtures, examples, or call sites
- original JS/TS output can be generated
- Rust/NAPI output equals original JS/TS output
- benchmark compares equivalent work

If agent-provided context cannot be verified, return `BLOCKED_AGENT_CONTEXT_UNVERIFIED`.

## Test Provenance And Hallucination Gate

Agent-created tests are useful, but they are not trusted oracles by default.

Rustify must record a baseline before asking Codex for context:

- `git rev-parse HEAD`
- `git status --porcelain`
- tracked file list from `git ls-files`
- hashes for candidate tests, fixtures, snapshots, and source files
- run start timestamp

Then classify evidence:

- `baseline_committed`: tracked and unchanged before Rustify asked the agent. Strongest.
- `baseline_dirty`: existed before the run but was already modified/untracked. Medium, requires user/agent explanation.
- `created_during_run`: added after the context request. Weak; treat as proposed harness only.
- `modified_during_run`: changed after the context request. Weak unless change is explicitly approved.

Running a test is required, but not enough. A bad test can pass without testing the target function.

For a proposed or weak test, Rustify must verify:

- the test actually imports/calls the candidate function, using AST/call trace/coverage
- the test fails if Rustify temporarily mutates function A to throw or return a wrong value
- expected output is produced by the original JS/TS function or comes from an existing committed fixture/snapshot
- Rust/NAPI output equals that original JS/TS output

If an agent writes both the input and expected output from imagination, Rustify must not call that an oracle. It can run it as a smoke harness only and should return `BLOCKED_ORACLE_REQUIRED` for migration.

## Future, Not Today

Do not build these before the CLI proof loop works:

- full Codex plugin
- MCP server
- Codex App Server integration
- desktop app
- pet visualizer
- presentation generator
- non-JS/TS adapters

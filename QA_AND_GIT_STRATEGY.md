# QA And Git Strategy

This repo is the CAR/Codex Auto Runner lane. It must stay independently runnable and pullable by teammates.

## QA Strategy

QA is not a polish phase only. Every ticket should leave machine-checkable evidence.

Minimum checks before each pushed milestone:

```text
git status --short
git diff --check
python3 .codex-autorunner/bin/lint_tickets.py if tickets changed and script exists
npm run build        if package.json exists and script exists
npm test             if package.json exists and script exists
cargo test/build     if Cargo project exists
rustify demo         when CLI exists
rustify auto <sample-repo> --mode conservative --json when auto path exists
```

If a command does not exist yet, record that as `not available yet`, not as a failure.

Required product QA gates:

- `rustify inspect <repo> --json` emits valid JSON.
- `rustify demo` writes `.rustify/runs/<run-id>/report.md`.
- happy path proves: candidate -> oracle -> Rust/NAPI -> equality -> benchmark -> report.
- blocker path returns `BLOCKED_*` with exit code 0.
- infrastructure crashes return nonzero with `ERROR_TOOL_FAILURE`.
- non-JS/TS repo returns `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- agent-created tests are treated as weak until provenance, call trace/coverage, mutation check, original JS/TS oracle, and Rust/NAPI equality are verified.

Final pre-submit QA:

```text
NO NEW FEATURES after the stop point.
Run demo from cold terminal.
Open proof report.
Run blocker case.
Run compare if both repos exist.
Confirm CAR ticket evidence matches actual CLI output.
```

## PR Strategy

Main is the stable showcase branch. Keep it pullable and demo-safe.

Do not stack risky work directly on `main`. Use one child branch/PR per stage:

```text
codex/01-cli-envelope
codex/02-artifact-writer
codex/03-papaparse-oracle
codex/04-napi-verify-bench
codex/05-blocker-path
codex/06-second-target
codex/07-compare-demo
```

Each stage must be merged before starting the next stage unless the user explicitly approves parallel branches.

Stage flow:

```text
pull main
create child branch
implement one stage
run stage QA
open PR or prepare PR summary
verify acceptance criteria
merge to main
pull main
start next child branch
```

Use small verified commits:

```text
docs: ...
feat: ...
test: ...
fix: ...
chore: ...
```

Before each PR:

```text
git status --short
git diff --check
run strongest available QA commands
git diff
```

Push the child branch:

```text
git add <changed-files>
git commit -m "<type>: <short outcome>"
git push -u origin codex/<stage>
```

Merge only after QA passes or the PR clearly documents a controlled blocker.

After merge:

```text
git switch main
git pull --ff-only
```

Do not force-push. Do not rewrite public history during the hackathon.

Emergency exception: if demo is inside the final buffer and GitHub PR flow is too slow, commit directly to `main` only for demo-breaking fixes, then immediately record what was changed and what command verifies it.

## Handoff Rule

Every pushed milestone should answer:

```text
What works?
What is blocked?
What command proves it?
Where is the artifact?
```

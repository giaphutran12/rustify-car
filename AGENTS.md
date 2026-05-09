# Rustify CAR Lane

## Mission
- Build Rustify from scratch as a hackathon prototype.
- Optimize for Track 2 Engineering Depth and Secret Track: Best Use of Codex.
- CAR is part of the build story and may be part of the demo, but the product must stand on its own as a working CLI.

## Hard Rules
- Do not inspect, print, summarize, diff, or log real env files. `.env.example` only.
- Prefer small, verifiable changes.
- Every ticket must leave concrete evidence: command output, report file, screenshot, benchmark JSON, or explicit blocker.
- If proof fails, say `BLOCKED` with reason. Do not fake success.

## Product Rule
- Rustify migrates only proven hot-path functions.
- File-level migration is not enough.
- Required gate: profiler or benchmark proves the exact function burns CPU before migration.
- Required gate: behavior oracle proves Rust output matches original JS/TS output.
- Required gate: benchmark proves before/after impact or reports honest no-win.

## CAR Lane Rule
- Use `.codex-autorunner/contextspace/spec.md` as source of truth.
- Tickets live in `.codex-autorunner/tickets/`.
- Keep ticket frontmatter `done: false` until verified.
- Run `python3 .codex-autorunner/bin/lint_tickets.py` after ticket edits.
- Use CAR for parallelizable work: scaffolding, docs, report renderer, fixtures, demo data, UI garnish.
- Keep critical algorithm decisions written in `.codex-autorunner/contextspace/decisions.md`.

## Demo Rule
- Demo must show one full path working:
  `profile -> candidate -> boundary -> oracle -> Rust -> verify -> bench -> report`.
- Optional polish: desktop shell, pet visualizer, generated presentation image.
- Polish must not steal time from working proof loop.

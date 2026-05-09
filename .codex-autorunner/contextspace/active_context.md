# Active Context

Timebox: May 9 hackathon, build starts 11:30 Vietnam time, submit by 16:00.

Current strategic choice:
- Build two lanes from scratch.
- `rustify-car`: CAR-autonomous ticket lane.
- `rustify-codex`: Codex Mac app critical-path lane.
- Keep both independent until benchmark/selection.

Core product:
- Rustify migrates one proven JS/TS hot-path function to Rust.
- Product is adapter-based, but hackathon implementation supports JS/TS only.
- Non-JS/TS repos return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- Function-level proof required.
- JS behavior oracle required.
- Runtime context required before auto-profile/migration.
- If proof fails, block honestly.

Immediate priority:
1. Get Rust/NAPI scaffold.
2. Get PapaParse-style CSV fast-mode happy path.
3. Get `rustify demo` working end to end.
4. Add oh-my-openagent-style second verified target if time.
5. Add opencode as high-star blocked/candidate target if no safe oracle/profile.
6. Explore WASM in parallel if cheap.
7. Keep CLI bridge only as last-hope fallback/test harness.
8. Only then add desktop/pet/presentation polish.

Do not chase:
- whole-app rewrite
- full GUI
- Temporal integration
- iii clone
- arbitrary JS transpiler

CAR UI:
- Hub URL: http://127.0.0.1:8765
- Repo id: rustify-car
- Repo path: /Users/edwardtran/car-hub/rustify-car

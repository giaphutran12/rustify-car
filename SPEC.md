# Rustify CAR Lane

Canonical CAR spec:

```text
/Users/edwardtran/car-hub/rustify-car/.codex-autorunner/contextspace/spec.md
```

This root file exists so humans and Codex Mac app can find the plan quickly.

Summary:
- Build Rustify as a Rust CLI.
- Build NAPI as the primary bridge; CLI is only fallback/test harness.
- Product is adapter-based, but hackathon implementation supports JS/TS only.
- Non-JS/TS repos return `BLOCKED_UNSUPPORTED_SOURCE_LANGUAGE`.
- Migrate only proven JS/TS hot-path functions.
- Require runtime context, profile proof, boundary proof, oracle proof, benchmark proof.
- No oracle = no migration.
- Primary demos: PapaParse-style CSV fast mode, oh-my-openagent hashline, opencode candidate/blocker.
- Use CAR tickets for autonomous work.
- Keep desktop/pet/presentation polish secondary.

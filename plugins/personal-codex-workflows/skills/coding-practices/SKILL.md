---
name: coding-practices
description: Use when AGENTS requires coding guidance, or when a code task needs non-trivial implementation architecture, ownership, dependency seams, testing strategy, backend/frontend structure, language, framework, or protocol implementation decisions. Use api for HTTP/REST contract, OpenAPI, auth/versioning contract, status/header, or RFC 9457 response-shape decisions.
---

# Coding Practices

Use this skill as the single installed coding guidance entry point. Keep this file small; load scoped references only when the task needs them.

## Base Rules

- Match the existing project style before introducing new conventions.
- Keep changes scoped to the requested behavior.
- Prefer clear ownership over clever abstraction.
- For non-trivial implementation, choose the code shape before editing. Place responsibilities where they naturally belong in the existing project structure.
- Prefer modules that change for one reason. Do not treat fewer files as better when that mixes presentation, state, domain rules, validation, persistence, transport, configuration, styling policy, or integration wiring.
- If the requested scope, allowed paths, or current structure would force unrelated concerns together, pause and report the structural conflict before implementing.
- Name domain concepts, protocol values, limits, defaults, and operational constants.
- Avoid vague buckets such as `utils`, `helpers`, or `common` unless the module has a clear contract.
- Add abstractions only when they remove real duplication, isolate a real boundary, or make testing materially cleaner.
- Keep boundary parsing, validation, and conversion separate from core logic.
- Keep public APIs intentional and small.
- Add dependencies only when they remove meaningful complexity or provide mature behavior that is risky to hand-roll.
- Comment non-obvious decisions, invariants, tradeoffs, and operational constraints; do not comment what the code already says.

## Reference Routing

Read only the branches that apply. Branches are composable; a task may need one area branch plus one language branch plus one framework/library or protocol branch.

Area branches:

- Backend architecture, services, APIs, repositories, outbound clients, or backend tests: `references/areas/backend/INDEX.md`
- Frontend product/UI behavior, accessibility, layout, forms, tables, or browser verification: `references/areas/frontend/INDEX.md`

Language branches:

- Rust: `references/languages/rust/INDEX.md`
- Go: `references/languages/go/INDEX.md`
- Python: `references/languages/python/INDEX.md`
- TypeScript/JavaScript: `references/languages/typescript/INDEX.md`

Framework/library branches:

- Svelte/SvelteKit: `references/frameworks/svelte/INDEX.md`
- Tonic: `references/frameworks/tonic/INDEX.md`
- SQLx: `references/frameworks/sqlx/INDEX.md`

Protocol branches:

- HTTP/API: `references/protocols/http/INDEX.md`
- gRPC: `references/protocols/grpc/INDEX.md`

Examples:

- Rust backend service: backend area + Rust language.
- Rust gRPC service with Tonic: backend area + Rust language + gRPC protocol + Tonic framework.
- Svelte UI: frontend area + TypeScript language + Svelte framework.
- TypeScript backend API: backend area + TypeScript language + HTTP/API protocol.

Do not read every branch by default. Follow the path needed for the task.

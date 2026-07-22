---
name: coding-practices
description: Use when AGENTS requires coding guidance, or when a code task needs non-trivial implementation architecture, ownership, dependency seams, testing strategy, backend/frontend structure, language, framework, or protocol implementation decisions. Use api for HTTP/REST contract, OpenAPI, auth/versioning contract, status/header, or RFC 9457 response-shape decisions.
---

# Coding Practices

Use this skill as the single installed coding guidance entry point. Keep this file small; load scoped references only when the task needs them.

## General Engineering

- Match existing repository style and keep changes scoped to the requested behavior before introducing a new convention.
- Name domain concepts, protocol values, limits, defaults, and operational constants instead of scattering unexplained primitives. Keep each named value with the owner of the rule or contract it expresses.
- Treat unexplained numeric literals as magic numbers. Name thresholds, sizes, timeouts, retry counts, breakpoints, layers, and protocol values with constants owned by the rule. Keep obvious indices and arithmetic identities inline when their meaning is self-evident.
- Keep public APIs intentional and small.
- Prefer existing language, platform, framework, and repository primitives. Add a dependency only when it removes meaningful complexity or supplies mature behavior that is risky to implement locally, including accessibility-sensitive interaction behavior that repository primitives do not already provide.
- Do not create vague cross-owner buckets such as `utils`, `helpers`, or `common`. Use a named responsibility or a repository-designated neutral primitive family.
- Comment non-obvious decisions, invariants, tradeoffs, and operational constraints; do not narrate self-explanatory code.

## Ownership And Change Shape

- Before a non-trivial implementation, identify each changed responsibility's owner, allowed dependency direction, trust or representation boundary, domain-safe value that must survive, and test seam. A change is non-trivial when it crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior.
- Keep code with the narrowest repository-designated owner whose contract matches its responsibility. Treat the full path as naming context, and create directories only for real named responsibilities.
- Promote code only to a matching designated owner with a real consumer or a deliberately established neutral primitive family. A generic name, repeated import, large file, or predicted reuse is not promotion evidence.
- Split at stable responsibility seams. File size, file count, and directory count are audit signals, not automatic split or promotion thresholds.
- If a change requires a forbidden dependency, an undefined owner, duplicated validation, or loss of a domain-safe value, stop and report the structural conflict. Do not hide it behind a barrel, callback, cast, locator, or generic wrapper.
- Pass replaceable external clients, clocks, and runtime services through an explicit contract at the owning composition seam. Do not hide them behind a global registry or service locator.
- Do not inject a pure deterministic helper merely for stylistic symmetry. Call it directly unless substitution, side effects, runtime context, or an actual ownership boundary makes a dependency seam necessary.

## Contracts And Tests

- Decode data where trust or representation changes. Call construction owned by the invariant's domain contract, then retain the domain-safe value through internal layers until another boundary requires conversion.
- Introduce an abstraction or type parameter only when it preserves a real relationship, isolates an external dependency, supports substitutable implementations, or implements a reused algorithm. Otherwise prefer the concrete owner and contract.
- Follow the repository's test-location convention, but make test responsibility reveal the production owner and boundary seam. Add compile-time tests when the type system carries an invariant runtime tests cannot prove.

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

For frontend work that meets the non-trivial definition above, load the frontend area index, every focused frontend reference matching the changed concerns, and the active language and framework indexes before editing. From those indexes, load only the focused references selected by the changed concerns or an explicit `Also load` instruction; activation alone does not load an entire branch. The area owns frontend architecture, the language owns type-system mechanics, and the framework maps those rules onto framework files and lifecycle seams.

Do not read every branch by default. Follow the path needed for the task.

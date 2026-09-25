---
name: coding-practices
description: Use before writing, refactoring, or reviewing code whenever the change crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior, or when project instructions require coding guidance. Covers implementation architecture, ownership, dependency seams, testing strategy, backend/frontend structure, and language, framework, or protocol implementation decisions. Use api for HTTP/REST contract, OpenAPI, auth/versioning contract, status/header, or RFC 9457 response-shape decisions.
---

# Coding Practices

Router only. Every rule has exactly one owning file; this one owns none.

## Scope Test

**Non-trivial** means the change crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior. Adding code to a file already over 1000 lines, or adding a new behavior area to a test file, is also non-trivial.

Anything else is trivial: match repository style, proceed, load nothing. Guidance elsewhere saying "substantial" or "significant" means this definition.

## Load

1. Non-trivial work always loads [Core Practices](./references/core.md), for the universal gates and the change map.
2. Then every branch matched below. Branches compose. Activating a branch does not load all of it; each index selects its own leaves.

| Branch | Load when |
| --- | --- |
| [Backend](./references/areas/backend/INDEX.md) | services, transport handlers, repositories, outbound clients, backend tests |
| [Frontend](./references/areas/frontend/INDEX.md) | UI behavior, accessibility, layout, forms, tables, browser verification |
| [Rust](./references/languages/rust/INDEX.md) · [Go](./references/languages/go/INDEX.md) · [Python](./references/languages/python/INDEX.md) · [TypeScript](./references/languages/typescript/INDEX.md) | the language being written |
| [Svelte](./references/frameworks/svelte/INDEX.md) · [Tonic](./references/frameworks/tonic/INDEX.md) · [SQLx](./references/frameworks/sqlx/INDEX.md) | the framework being used |
| [HTTP](./references/protocols/http/INDEX.md) · [gRPC](./references/protocols/grpc/INDEX.md) | the wire protocol being defined |
| [Readability](./references/readability.md) | writing code: naming a value, restructuring a branch, or commenting a decision |
| [Function Shape](./references/function-shape.md) | writing or reviewing a function: deciding what it needs to run, or splitting a function that mixes decision and effect |
| [Dependency Management](./references/dependency-management.md) | adding, replacing, or removing a dependency |

Typical compositions: Rust backend service → backend + Rust. Rust gRPC with Tonic → backend + Rust + gRPC + Tonic. Svelte UI → frontend + TypeScript + Svelte. TypeScript backend API → backend + TypeScript + HTTP.

## Precedence

Area owns responsibility placement and dependency direction. Language owns type-system and syntax mechanics. Framework maps both onto its files and lifecycle. Protocol owns wire contracts. When two branches appear to cover the same rule, the narrower one is wrong and should link, not restate.

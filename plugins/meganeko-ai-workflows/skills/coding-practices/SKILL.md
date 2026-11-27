---
name: coding-practices
description: Use before designing a new project or proposing a codebase structure, folder layout, module split, or architecture document, even when no code is written yet, and before writing, changing, refactoring, or reviewing code, however small. Naming, comments, control flow, and function shape apply to every code change, including a one line fix or a rename. For a change that crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior, it also covers who decides structure, ownership, dependency direction, boundaries, test seams, backend, frontend, and command line structure, and language, framework, and protocol rules. Use api for the HTTP error body (RFC 9457 problem+json) and its OpenAPI schema.
---

# Coding Practices

Router, plus the Scope Test below. Every other rule has exactly one owning file.

## Every Code Change

Before any code change, however small, load [Readability](./references/readability.md) and [Function Shape](./references/function-shape.md). They hold the rules that make code explain itself without comments: names, constants, control flow, comments, the order a new function is written in, and one job per function. A change that adds a function, or changes which function calls which, also loads [What Exists](./references/what-exists.md), for listing the functions and types a module and a library already hold.

## Scope Test

**Non-trivial** means the change crosses files or owners, introduces a contract or abstraction, or changes state, persistence, transport, framework, or domain behavior. Adding code to a file already over 1000 lines, or adding a new behavior area to a test file, is also non-trivial.

Anything else is trivial: match repository style, and load nothing beyond what Every Code Change names. Guidance elsewhere saying "substantial" or "significant" means this definition.

## Load For Non-Trivial Work

1. [Core Practices](./references/core.md), for who decides structure, the change map, and the universal gates.
2. Every branch matched below. Branches compose. Activating a branch does not load all of it; each index selects its own leaves.
3. [Architecture Document](./references/architecture-document.md), only when the change writes or changes the project's architecture document.
4. When designing a new project, before proposing any structure: 1 and 2 above, plus the layout its area branch owns. For a backend that is [Backend Project Layout](./references/areas/backend/project-layout.md).

| Branch | Load when |
| --- | --- |
| [Backend](./references/areas/backend/INDEX.md) | services, transport handlers, repositories, outbound clients, backend tests |
| [Frontend](./references/areas/frontend/INDEX.md) | UI behavior, accessibility, layout, forms, tables, browser verification |
| [Command Line And Library](./references/areas/cli/INDEX.md) | a command line tool, a script, a hook or filter program, or a library with no service and no UI |
| [Rust](./references/languages/rust/INDEX.md) · [Go](./references/languages/go/INDEX.md) · [Python](./references/languages/python/INDEX.md) · [TypeScript](./references/languages/typescript/INDEX.md) | the language being written |
| [Svelte](./references/frameworks/svelte/INDEX.md) · [React](./references/frameworks/react/INDEX.md) · [Tonic](./references/frameworks/tonic/INDEX.md) · [SQLx](./references/frameworks/sqlx/INDEX.md) | the framework being used |
| [HTTP](./references/protocols/http/INDEX.md) · [gRPC](./references/protocols/grpc/INDEX.md) | the wire protocol being defined |
| [Dependency Management](./references/dependency-management.md) | adding, replacing, or removing a third-party library that was approved |

Load only a file that a routing table selects for your change, or one named after **Also load**. Any other link is a citation: open it only when you need the rule it points at.

Typical compositions: Rust backend service → backend + Rust. Rust gRPC with Tonic → backend + Rust + gRPC + Tonic. Svelte UI → frontend + TypeScript + Svelte. React UI → frontend + TypeScript + React. TypeScript backend API → backend + TypeScript + HTTP. Go command line tool → command line + Go.

## Precedence

Area owns responsibility placement and dependency direction. Language owns type-system and syntax mechanics. Framework maps both onto its files and lifecycle. Protocol owns wire contracts. When two branches appear to cover the same rule, the narrower one is wrong and should link, not restate.

A project's own instructions, and the brief or task you were given, outrank this skill wherever they are more specific.

---
name: api
description: "Use when defining, implementing, or reviewing the error responses of an HTTP API: the RFC 9457 application/problem+json body, stable error codes and categories, field level validation errors, the single formatter at the HTTP boundary, and the OpenAPI schema for error responses, with starter code for TypeScript, Go, Python, and Rust. For routes, methods, status code choice, headers, versioning, authentication, and service structure, use coding-practices."
---

# API

This skill owns one thing: what an HTTP API's error response looks like and where it is produced. Everything else about an HTTP contract (methods, status code choice, headers, versioning, authentication) is owned by the HTTP branch of the `coding-practices` skill.

## Problem JSON

For RFC 9457 Problem Details (`application/problem+json`), read `references/problem-json/index.md` first. It holds the response shape, the error categories, and the rules.

Then read only what the task needs:

| Task | Load |
| --- | --- |
| Writing or reviewing an OpenAPI document | `references/problem-json/openapi.md` |
| Telling the standard's own fields from this contract's extensions, or relating RFC 9457 to RFC 7807 | `references/problem-json/rfc-9457.md` |
| Implementing in TypeScript | `references/problem-json/typescript.md` |
| Implementing in Go | `references/problem-json/go.md` |
| Implementing in Python | `references/problem-json/python.md` |
| Implementing in Rust | `references/problem-json/rust.md` |

Core rule: no handler builds an error body by hand. The HTTP boundary maps every failure to a typed problem category, and one formatter turns that into the response.

The types on these pages carry an HTTP status, so they are boundary code. Code inside a service raises product errors that know nothing about HTTP; that rule, and where the mapping sits, belong to the backend branch of `coding-practices`.

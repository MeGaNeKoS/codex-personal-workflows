---
name: api
description: "Use for HTTP/REST API contract decisions: public route/resource design, request/response DTO shapes, OpenAPI schemas, authentication/versioning contract, status codes, headers, and RFC 9457 application/problem+json response formats. Prefer coding-practices for implementation architecture, service/module boundaries, repositories, dependency seams, and tests."
---

# API

Use this skill for HTTP/REST API contracts and error-format decisions.

## Problem JSON

For RFC 9457 Problem Details / `application/problem+json`, read:

- `references/problem-json/index.md` for the shared model and rules.

Then read only what the task needs:

- `references/problem-json/openapi.md` when writing or reviewing OpenAPI schemas.
- `references/problem-json/rfc-9457.md` when checking standard/RFC compatibility details.
- The implementation file for the language in use:
  - `references/problem-json/typescript.md`
  - `references/problem-json/rust.md`
  - `references/problem-json/go.md`
  - `references/problem-json/python.md`

Core rule: do not manually build ad hoc JSON error bodies in individual handlers. Create typed application errors and format them once at the HTTP boundary.

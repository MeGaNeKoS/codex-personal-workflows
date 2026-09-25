# HTTP Caching And Rate Limits

Use when adding cache headers, conditional requests, or rate limiting.

## Caching

- Use RFC 9111 for HTTP caching. RFC 9111 obsoletes RFC 7234.
- Use `Cache-Control`, `ETag`, `Last-Modified`, and conditional requests where useful.
- Avoid caching private data in shared caches.

```text
Cache-Control: public,  max-age=3600   shared caches may store it
Cache-Control: private, max-age=300    browser only, never a CDN
Cache-Control: no-store                never written to any cache
```

The failure that matters: a per-user response served with `public` is served to the next user by a shared cache. If the response varies by identity, it is `private` or `no-store`.

## Rate Limits

- For exceeded limits, return `429 Too Many Requests` and `Retry-After` where useful.
- The `RateLimit-*` header family has changed across drafts and deployments. Verify the current project or API gateway standard before treating a specific syntax as mandatory.
- Prefer documenting the exact emitted rate-limit headers in the API contract.

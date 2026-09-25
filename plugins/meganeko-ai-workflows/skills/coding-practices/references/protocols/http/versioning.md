# HTTP Versioning And Deprecation

Use when introducing an API version, changing a public field, or removing an endpoint.

- API versioning has no single RFC. Prefer an explicit project policy.
- Path versioning such as `/api/v1/users` is simple and operationally clear.
- For deprecation, use the `Deprecation` header (RFC 9745), the `Sunset` header (RFC 8594), and `Link` relations (RFC 8288) when useful.
- Use deprecation and sunset headers with migration links when removing or replacing APIs.

```http
Deprecation: @1735689600
Sunset: Sat, 01 Nov 2025 00:00:00 GMT
Link: <https://docs.example.com/migrate/v2>; rel="deprecation"
```

Announce through headers before removal, not at removal. A client that only discovers the change when the endpoint returns 404 had no migration window.

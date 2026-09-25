# HTTP Authentication And Browser Security

Use when adding or changing authentication headers, token handling, CORS, or transport security.

## Authentication

- Use RFC 6750 Bearer token syntax: `Authorization: Bearer <token>`.
- For JWT payloads, follow RFC 7519 claims.
- For OAuth 2.0 flows, use RFC 6749 architecture and current OAuth security best practice.
- Public OAuth clients should use PKCE and strict redirect URI validation.

The recurring bug:

```text
exp, iat, nbf are NumericDate = SECONDS since epoch, not milliseconds.

  Date.now()            -> 1735689600000   wrong, 1000x too large
  Date.now() / 1000 | 0 -> 1735689600      correct
```

A token minted with millisecond `exp` validates for ~50,000 years. Nothing fails loudly.

## Browser Security

- Never use `Access-Control-Allow-Origin: *` with credentials.
- Prefer explicit CORS origins and expose only required methods and headers.
- Use RFC 6797 Strict Transport Security in production HTTPS deployments where appropriate.
- Only use HSTS `preload` when the domain owner has accepted the operational commitment. Preload removal is slow and affects every subdomain.

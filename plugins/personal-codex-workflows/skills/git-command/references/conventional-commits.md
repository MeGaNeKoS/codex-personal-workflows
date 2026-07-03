# Conventional Commits

Use Conventional Commits for commit subjects.

Use:

```text
<type>(<scope>): <summary>
```

Use `<type>: <summary>` only when a useful scope is not clear.

Common types:

- `feat`
- `fix`
- `refactor`
- `docs`
- `test`
- `chore`

Choose scopes from the touched ownership boundary, such as:

- `workspace`
- `proto`
- `control`
- `control-http`
- `control-grpc`
- `ingest`
- `ingest-grpc`
- `writer`
- `query`
- `authz`
- `audit`
- `license`
- `security`
- `quality`

Project-local scopes are allowed when they better describe the boundary, such as `auth`, `repository`, `management`, `bootstrap`, `errors`, `lifecycle`, `deploy`, or `docs`.

Keep the summary short, specific, imperative, and lowercase after the type/scope.

Examples:

```text
chore(workspace): initialize internal portal api workspace
feat(auth): add internal login and jwks endpoints
feat(management): add internal authority management api
refactor(repository): split postgres storage boundaries
docs(deploy): document dev bootstrap flow
```

## Body

Add a short body only when the subject alone does not explain the reason, impact, migration concern, operational risk, security boundary, lifecycle behavior, or non-obvious tradeoff.

Do not add a body just to restate the diff.

Use a small paragraph, not a long changelog. Keep it focused on why the change exists, what boundary it protects, or what operators must know.

Example:

```text
feat(bootstrap): add explicit internal portal bootstrap tool

Keep first-user and authority seeding outside the API startup path so production
deployments can run migrations and bootstrap as deliberate operational steps.
```

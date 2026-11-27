# Conventional Commits

Every commit subject follows Conventional Commits:

```text
<type>(<scope>): <summary>
```

## Type

| Type | Use for |
| --- | --- |
| `feat` | behavior a user or caller can newly observe |
| `fix` | wrong behavior corrected |
| `refactor` | structure changed, behavior unchanged |
| `perf` | same behavior, faster or smaller |
| `test` | tests only |
| `docs` | documentation only |
| `build` | build system, packaging, or third-party libraries |
| `ci` | pipeline configuration |
| `chore` | repository upkeep that fits none of the above |

A change that breaks callers puts `!` before the colon and adds a `BREAKING CHANGE:` footer that says what breaks.

## Scope

The scope names the owner of the change: the package, module, service, or top level directory the commit touches.

- Use the scopes the repository's history already uses. `git log --format=%s -30` shows them. Reuse one before inventing one.
- Where the history has none, use the directory or package name as the repository spells it.
- A commit that touches several owners for one reason takes the owner the change is about. If no single owner fits, write `<type>: <summary>` with no scope.
- A commit that needs two scopes because it does two things is two commits.

## Summary

Imperative mood, lowercase after the colon, no trailing period, and a subject line of at most 72 characters. Say what the commit does to the product, not which files changed.

```text
feat(auth): add refresh token rotation
fix(invoice): round tax per line instead of per total
refactor(storage): split order queries from the connection pool
docs(deploy): document the migration step
```

## Body

Write a body only when the subject cannot carry one of these:

- why the change exists, when the diff does not show it;
- what a caller, an operator, or a migration must do differently;
- a security or data consequence.

No body that restates the diff. One short paragraph, wrapped at 72 columns, with a blank line between it and the subject.

```text
feat(bootstrap): add an explicit bootstrap command

Keep first-user seeding out of the API startup path, so a production
deploy runs migrations and bootstrap as deliberate steps.
```

Where the repository has its own rule for footers, sign-off, or attribution lines, follow it.

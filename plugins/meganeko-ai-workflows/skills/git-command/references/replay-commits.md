# Replay Commits

Use this when initializing Git history from an already-finished or partially-finished tree.

Create replay-style commits instead of one giant final-state commit.

- Stage coherent ownership groups such as workspace setup, schema, repository, auth, management API, bootstrap tooling, deployment docs, error handling, and lifecycle.
- Prefer conceptual, reviewable commits over perfect chronological archaeology.
- Use Conventional Commit subjects for each replay commit.
- Add short bodies only for security, deployment, migration, lifecycle, bootstrap, or architecture boundary context.
- Adjust author and committer dates when requested so the history reads like work progressed over time.
- Avoid committing generated build output, local secret files, transient tool output, or ignored dev state. Verify `.gitignore` before staging.
- If the user requires every replay commit to build, say that this takes longer and verify each commit. Otherwise, verify the final reconstructed history.

Suggested sequence for a finished backend service:

```text
chore(workspace): initialize service workspace
feat(schema): add database schema and migrations
feat(repository): add storage access layer
feat(auth): add login and token issuance
feat(management): add management api
feat(bootstrap): add explicit bootstrap tool
feat(deploy): add dev docker integration
feat(errors): add problem json error contract
feat(lifecycle): add readiness and panic boundary
docs: document local usage and operational behavior
```

Adapt scopes and ordering to the actual tree.

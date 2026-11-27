# Replay Commits

Use this when a tree that is already finished, or partly finished, has to become a Git history. Build a series of commits a reviewer can read one at a time, not one commit holding the final state.

- One commit per owner or concern, in the order a reader needs them: project setup first, then each layer or feature before the code that depends on it, documentation last. Take the groups from the tree's own top level structure.
- Prefer a commit that makes sense on its own over reconstructing the order the work really happened in.
- Subjects, scopes, and bodies follow `conventional-commits.md`. Staging follows Safety in `SKILL.md`: name the files of each group, and check `.gitignore` before the first `git add`.
- Set author and committer dates only when the user asks for them, with `GIT_AUTHOR_DATE` and `GIT_COMMITTER_DATE` on each commit.
- Whether every commit must build is the user's choice. When they want it, build at each commit and say that this takes longer. Otherwise build the final commit only.
- When the last group is committed, `git status --short` prints nothing: every file of the tree that is not ignored is in the history.

Example for a backend service. Take the scopes and the order from the actual tree:

```text
chore(workspace): initialize service workspace
feat(schema): add database schema and migrations
feat(storage): add storage access layer
feat(auth): add login and token issuance
feat(api): add management endpoints
feat(deploy): add container build
docs: document local usage
```

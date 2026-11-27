# History Edits

Use this when amending, rebasing, or otherwise rewriting commits. Safety in `SKILL.md` says who has to ask: the user or the task for a commit that exists only in this clone, the user by name for a commit that has been pushed.

- Check first whether the commits were pushed. `git status -sb` shows how far the branch is ahead of and behind its upstream, and `git branch -r --contains <commit>` lists the remote branches that hold a commit.
- Before rewriting more than one commit, create a backup branch: `git branch backup/<name>`.
- Use non-interactive forms: `git commit --amend --no-edit`, `git rebase --onto <new-base> <old-base>`, or `git commit --fixup <commit>` followed by `GIT_SEQUENCE_EDITOR=true git rebase -i --autosquash <base>`, which runs the rebase without opening the todo list. Continue a stopped rebase with `GIT_EDITOR=true git rebase --continue`.
- An amend or a rebase keeps each commit's author date and moves its committer date to now. That is the normal result: leave it. Do not pass `--reset-author` or `--date` unless the user asks.
- Change dates only when the user asks, and tell them what the command sets. `git rebase --committer-date-is-author-date` copies each commit's author date into its committer date. `GIT_COMMITTER_DATE` gives one and the same date to every commit the command creates.
- After the rewrite, compare trees: `git diff backup/<name> HEAD` prints nothing when only the history changed. Then read `git log --oneline` for the rewritten range, and the log of any path the rewrite was about.
- A backup branch keeps the old commits reachable. Delete it when the user confirms the result.
- When the rewrite removed a secret or an unwanted file, the old content is gone from this clone only after the backup branch and any `refs/original/` refs are deleted, the reflogs are expired (`git reflog expire --expire=now --all`), and `git gc --prune=now` has run. Expiring the reflogs also drops stash entries, which live in a reflog: run `git stash list` first and tell the user what will be lost. Run those three only after the user confirms. Every other clone and the remote still hold the old commits, and a secret that was ever pushed has to be rotated: say both.

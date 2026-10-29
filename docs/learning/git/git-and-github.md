# Git and GitHub

*Learned 2026-10-27 to 2026-10-28 (Week 1, Day 2). Coming from Bitbucket's web interface, where I'd made PRs at work.*

## Git vs. GitHub

| | Git | GitHub |
|---|---|---|
| What | A version-control program on my machine | A website that hosts Git repositories |
| Offline | Yes | No |
| Provides | Commits, branches, history, remotes | Pull requests, reviews, Actions (CI), issues, a web UI |

**Pull requests are not a Git feature.** They're built by the hosting service (GitHub, Bitbucket, GitLab) on top of Git. That's why the PR command is `gh pr create` (the GitHub CLI), not `git ...`. The name comes from `git request-pull`, an old command that writes an email asking someone to pull your branch.

> **Data analogy:** a commit is an immutable snapshot with an ID and a parent pointer, like a Delta Lake or Iceberg snapshot you can time-travel to. GitHub is the remote storage the local repo syncs with.

## Core concepts

- **Commit:** a snapshot of the whole project, plus an author, a message and a parent. Its ID is a hash of all of that, so changing anything (even the author's email) produces a new ID.
- **Branch:** a movable label pointing at one commit. Creating one copies no files, so it's instant. Committing moves the current branch's label forward.
- **`HEAD`:** "where I am now," usually the current branch.
- **Remote:** a named link to another copy of the repo. `origin` is the conventional name for the main one. `git remote -v` lists them; fetch and push URLs can differ but usually don't.
- **Upstream (tracking branch):** the remote branch that a local branch syncs with. Set it once with `git push -u origin <branch>`. After that, plain `git push` and `git pull` work, and `git status` reports "ahead/behind."

> **Data analogy:** a branch is a named pointer to a snapshot (like an Iceberg branch or tag). No data is copied.

## The three places changes live

```
Working directory ──git add──→ Staging area ──git commit──→ History (HEAD)
     └──── git diff ────┘          └─── git diff --staged ───┘
     └──────────────────── git diff HEAD ────────────────────┘
```

- After `git add`, plain `git diff` is **empty**. The changes are now in staging, so check them with `git diff --staged`.
- `git add` stages a **snapshot at that moment**. Later edits need another `git add`.

## Reading a diff

```diff
diff --git a/README.md b/README.md     ← a = before, b = after
--- a/README.md
+++ b/README.md
@@ -8,7 +8,7 @@                         ← before: starts at line 8, 7 lines; after: line 8, 7 lines
 ## Status                              ← space = unchanged context (3 lines by default)
-**Planning phase.** No application code yet.     ← removed
+**Planning phase.** Finished day one ...         ← added
```

Diffs work on whole lines. A changed line shows up as a delete plus an insert, just as CDC (change data capture) often represents an update. `--word-diff` shows the changed words, `--stat` shows a summary, and `git show <commit>` shows what a past commit changed.

## `git add .` vs. `git add <file>`

- `git add README.md` stages that one file.
- `git add .` stages **everything** under the current directory: new, modified and deleted files. `git add -A` does the same for the whole repo, from any directory.
- `.gitignore` excludes patterns, but only the ones listed in it. Run `git status` first. `git add .` is the `SELECT *` of Git: convenient, but it eventually pulls in something unexpected, like a test file or an `.env` with secrets.

## `git checkout` vs. `git switch` / `git restore`

Git 2.23 (2019) split the overloaded `checkout` into two commands:

| Task | Old | New |
|---|---|---|
| Switch branch | `git checkout main` | `git switch main` |
| Create and switch | `git checkout -b x` | `git switch -c x` |
| Discard file changes | `git checkout -- file` | `git restore file` |

Both forms still work. The newer ones make it harder to discard work by accident.

A new branch starts from **the current commit**, unless a start point is given (`git switch -c x main`).

## The workflow (GitHub Flow)

See [ADR 0003](../../decisions/0003-git-workflow.md) for the rules this project follows.

```
1. Sync      git switch main && git pull
2. Branch    git switch -c <type>/<description>
3. Work      git status → git diff → git add <files> → git diff --staged → git commit -m "type: ..."
4. Push      git push -u origin <branch>
5. PR        gh pr create --base main --title "..." --body "..."    (or in the web UI)
6. Review    the "Files changed" tab
7. Merge     Squash and merge (UI) or gh pr merge <n> --squash --delete-branch
8. Clean up  git switch main && git pull && git branch -d <branch>
             git push origin --delete <branch>   (if GitHub still has it)
```

### Merge strategies

| Strategy | Result on `main` |
|---|---|
| Merge commit (the UI default) | The branch's commits plus a merge commit; history forks and rejoins |
| **Squash and merge** (this project's choice) | One commit per PR; history stays a straight line |
| Rebase and merge | The branch's commits replayed onto `main`, with no merge commit |

PR #1 was merged with a merge commit, which is how it shows up in `git log --graph`:

```
*   4a917b6 Merge pull request #1 ...
|\
| * e2932ee docs: update README status after Day 1
|/
* 845299c docs: wrap up Week 1 Day 1 ...
```

## Bitbucket to GitHub

| Bitbucket | GitHub |
|---|---|
| Diff tab | Files changed |
| Overview / Activity | Conversation |
| Close source branch | Delete branch |
| Pipelines | Actions |

On GitHub, a PR's author **can't approve their own PR**.

## `gh` (GitHub CLI) cheat sheet

| Command | Does |
|---|---|
| `gh auth login` / `gh auth status` | Log in (device flow, token stored in the keychain) / check the login |
| `gh repo create <name> --public --source=. --remote=origin` | Create a GitHub repo from a local one |
| `gh pr create` / `--web` | Open a PR / open the PR form in a browser |
| `gh pr list` / `gh pr view --web` / `gh pr diff` | Inspect PRs |
| `gh pr merge <n> --squash --delete-branch` | Merge a PR and clean up |

Interactive prompts don't work through Claude Code's `!` prefix (there's no keyboard input), so pass every option as a flag.

## Privacy before going public

- Commits record the author's **email**. Before the first push, all commits were rewritten to use the GitHub noreply address (`<id>+<login>@users.noreply.github.com`), and the repo-level `user.email` was set to it.
- Rewriting history **before** pushing is safe. Rewriting it **after** pushing forces everyone to reconcile their copies.
- Check for secrets and personal data (IP addresses, tokens) before every commit to a public repo.

## Gotchas I hit

- `dgh` → `command not found`. The shell searched `PATH` and found no such program.
- Interactive `gh auth login` through `!` hung waiting for input. Approving the device code in the browser still worked.
- Merging in the UI picked the default (merge commit) instead of squash. Choose from the dropdown.
- `gh pr merge --delete-branch` on an already-merged PR cleaned up locally but left the remote branch, so it needed `git push origin --delete <branch>`.

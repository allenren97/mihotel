# 0003. Git workflow: GitHub Flow, typed branches, Conventional Commits, squash merges

- **Date:** 2026-10-28
- **Status:** Accepted

## Context

The repo is public on GitHub (`allenren97/mihotel`) and serves as a portfolio. Changes come from two contributors: the owner and Claude. The history should be easy to read, `main` should always be in a working state, and the workflow should match how professional teams work. Until Day 2, the planning documents were committed straight to `main`.

## Options considered

1. **Commit directly to `main`.** The simplest option, but there's no review step, the history is noisy, and it doesn't reflect team practice.
2. **GitHub Flow:** short-lived branches off `main`, merged through pull requests. Simple, widely used, and suited to continuously deployed web apps.
3. **Git Flow:** long-lived `develop` and `release` branches. Built for versioned release cycles, and too heavy for a single web app.

## Decision

**GitHub Flow**, with these conventions:

- **Every change goes through a pull request.** No direct commits to `main` from 2026-10-28 on.
- **Branch names:** `<type>/<short-description>`, where the type is one of:
  - `feature/` for new functionality
  - `fix/` for bug fixes
  - `docs/` for documentation only
  - `chore/` for maintenance
- **Commit messages:** Conventional Commits, `type: description` (`feat`, `fix`, `docs`, `chore`, `refactor`, `test`), with an optional scope such as `docs(learning): ...`.
- **PR descriptions** have **What** and **Why** sections.
- **Merge strategy:** squash and merge, giving one commit per PR on `main`. Branches are deleted after merging.
- **Documentation changes** (journal, ADRs, learning notes) go in the same PR as the work they describe.
- **Author email:** the GitHub noreply address for this repo.

## Consequences

- `main`'s history reads as one commit per change, and each links to a PR with context.
- There's a little overhead for tiny changes. That's accepted, because practising the workflow is one of the project's goals.
- PR #1 was merged with a merge commit before this decision was written. It stays in the history as is.
- Possible follow-ups: restrict the repo to squash merging only, protect `main` against direct pushes, and run CI checks on PRs once tests exist (from Week 2).

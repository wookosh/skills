---
name: git-pr
description: Creates and manages pull requests via GitHub CLI (or equivalent). Use when the user asks to open, create, list, or update a PR—not for routine git push alone (git-push) or local commits (git-commit).
---

# Git PR Skill

You handle **pull requests**: create, list, view, and help fill title/body. You do not replace **`git-commit`** for staging and committing.

## Your Scope

- **`gh pr create`**, **`gh pr list`**, **`gh pr view`**, and related **`gh pr`** commands when GitHub CLI is available
- Ensuring the **branch exists on the remote** before opening a PR (coordinate with **`git-push`** if needed)
- Proposing **title** and **body** with the same confirmation pattern as commits (see below)

**Out of scope:** Routine `git push` without PR intent (use **`git-push`**); local `git add` / `git commit` (use **`git-commit`**).

## Prerequisites

- **`gh`** authenticated (`gh auth status`). If missing, explain how to install/auth or offer web-only steps.
- **Branch pushed:** If `origin/<branch>` is missing, ask whether to push first (delegate execution to **`git-push`** flow or run push with user confirmation—do not surprise-push).

## PR Creation Flow

### 1. Branch and remote

- Confirm current branch and that it is **not** the sole protected default when policy requires feature branches (read repo docs).
- If not pushed:
  ```
  Branch [name] is not on origin yet. Push before opening a PR?
  Reply: YES | NO
  ```
  If YES, complete push (see **`git-push`**), then continue.

### 2. Propose title and body

Use conventional title style unless the repo documents otherwise (e.g. `feat(scope): description`).

Propose a body template and adapt to the project:

```
## Summary
[What and why]

## Changes
- [Main changes]

## Checklist
- [ ] Tests pass (or N/A)
- [ ] Lint passes
- [ ] Docs updated if needed

## Related
- Branch: [branch name]
```

**Confirmation gate (same spirit as git-commit):**

```
═══════════════════════════════════════════════════════════
🔀 PR PROPOSAL
═══════════════════════════════════════════════════════════

Title: [proposed title]

Body:
---
[proposed body]
---

Reply with:
  SUBMIT  — Create the PR as proposed
  ALTER   — Revise title/body from feedback
  CANCEL  — Abort
═══════════════════════════════════════════════════════════
```

Wait for **SUBMIT** before running `gh pr create`.

### 3. Base branch and draft

- **Base:** default to the repo’s primary branch (`main` or as documented). Confirm if unsure.
- **Draft:** ask whether to open as **draft** or **ready for review**.

### 4. Create

```bash
gh pr create --title "..." --body "..." [--draft]
```

Show the **PR URL** in the report.

## Listing and viewing

- **`gh pr list`** — filter by head branch or author as needed.
- **`gh pr view [n]`** — show status, checks, reviewers.

## Rules

- **Never** create or merge PRs without user confirmation through the gate above (or an explicit one-shot instruction that restates title/body).
- If the project documents extra gates (design review, security), mention them in the PR body or checklist.
- Use **network** / **git_write** permissions as required by the tool environment.

## Report Format

```
✅ PR OPENED
   URL: [link]
   Branch: [head] → [base]
```

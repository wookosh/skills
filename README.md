# Agent skills

Shared **Cursor** and **Claude Code** skills for multiple repositories. Each skill lives in a directory named by its id (e.g. `git-commit/SKILL.md`).

## Skills

| Skill id | When to use |
|----------|-------------|
| **`git-commit`** | Stage changes, write commit messages, create **local** commits |
| **`git-push`** | Push commits to `origin`, set upstream, sync a branch (no PR authoring) |
| **`git-pr`** | Create/list/view **pull requests** (e.g. `gh pr create`), title/body confirmation |

**Routing:** Point agents (or a Cursor rule) so “commit / stage / message” loads **`git-commit`**, “push / publish branch” loads **`git-push`**, “open a PR / review request” loads **`git-pr`**.

## Layout (this repository)

```text
README.md
git-commit/
  SKILL.md
git-push/
  SKILL.md
git-pr/
  SKILL.md
```

Add more skills by adding sibling folders with their own `SKILL.md` and YAML frontmatter (`name`, `description`).

## How consuming projects install these skills

**Intended integration:** the **`solution-template`** CLI (or your org’s fork) — not a long-lived submodule path inside the app repo.

1. **Install / scaffold** — The CLI **checks out** this GitHub repository (clone or fetch into a cache directory — implementation detail), then **copies** each skill folder into the project’s tool paths, for example:
   - `git-commit/` → `.cursor/skills/git-commit/`
   - `git-push/` → `.cursor/skills/git-push/`
   - `git-pr/` → `.cursor/skills/git-pr/`
   - The same mapping under **`.claude/skills/`** when the user opts into Claude.

   The project **does not** keep a separate `.agent-skills` checkout; what you commit (or generate locally) are the **copies** under `.cursor/skills/` and `.claude/skills/`.

2. **Update skills** — A dedicated CLI command (e.g. **`solution-template update-skills`**) **pulls the latest `main`** from this repository on GitHub and **re-copies** the skill files into the same targets, overwriting the previous copies. Run it whenever you want to align with upstream skill changes.

3. **Tracking policy** (per project) — Either **commit** the copied `SKILL.md` files so everyone shares the same revision, or **gitignore** them and rely on each developer running install/update (document the choice in the app repo).

## Manual install (without the CLI)

Clone this repo somewhere temporary, then copy each `<skillId>/` directory into `.cursor/skills/<skillId>/` and/or `.claude/skills/<skillId>/`. Repeat after pulling new commits from `main`.

## Versioning

- Default source for the CLI is typically **`main`** on GitHub; tags or pinned SHAs may be added later for reproducible installs.
- Projects that **commit** copied skills can see upgrades as normal file diffs when someone runs **update skills** and commits.

## License

Add a license file to this repository when you publish it; consuming projects should respect its terms.

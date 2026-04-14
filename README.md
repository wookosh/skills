# Agent skills

Shared **Cursor** and **Claude Code** skills for multiple repositories. Each skill lives in a directory named by its id (e.g. `git-commit/SKILL.md`).

## Skills

| Skill id | When to use |
|----------|-------------|
| **`git-commit`** | Stage changes, write commit messages, create **local** commits |
| **`git-push`** | Push commits to `origin`, set upstream, sync a branch (no PR authoring) |
| **`git-pr`** | Create/list/view **pull requests** (e.g. `gh pr create`), title/body confirmation |

**Routing:** Point agents (or a Cursor rule) so “commit / stage / message” loads **`git-commit`**, “push / publish branch” loads **`git-push`**, “open a PR / review request” loads **`git-pr`**.

## Layout

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

## Using this repo in a project

### 1. Add the submodule

Add this repository as a **Git submodule** at **`.agent-skills`** in the consuming project (not directly under `.cursor/skills/<id>`—that would double the path segment and break resolution).

```bash
git submodule add <skills-repo-url> .agent-skills
```

After clone, teammates run:

```bash
git submodule update --init --recursive
```

### 2. Wire skills into Cursor / Claude (portable default: copy)

**Recommended for Mac and Windows:** copy each skill directory from the submodule into the tool’s skills folder so nothing depends on symlinks or OS privileges.

For each skill id you use (e.g. `git-commit`, `git-push`, `git-pr`):

- Copy **`.agent-skills/<skillId>/`** → **`.cursor/skills/<skillId>/`** (and optionally **`.claude/skills/<skillId>/`**).

You can do this manually, with a short script, or with a project template when your org adds automation. **Re-run the copy** whenever you update the `.agent-skills` submodule pointer so editors see the latest `SKILL.md` files.

Choose one policy for your repo:

- **Track copied files in Git** — everyone gets the same skill snapshots without extra steps after clone; you commit changes when you bump the submodule and re-copy.
- **Generate copies locally** — add copied paths to `.gitignore` and run a documented `sync-skills` script after clone/submodule update (good if you want zero skill files in the main tree).

### 3. Optional: symlinks (Unix-like only)

On **macOS** or **Linux**, you may symlink instead of copy to avoid duplicates, for example:

- `.cursor/skills/git-commit` → `../../.agent-skills/git-commit`

Use **relative** link targets. **Do not rely on symlinks as the only workflow:** on **Windows**, directory symlinks often require **Developer Mode** or elevated privileges, and Git’s handling depends on `core.symlinks`. Teams that must support Windows should prefer **copy** (section 2) unless everyone standardizes on WSL or symlink-capable environments.

## Versioning

Tag releases or use commit SHAs in submodule pointers so projects can pin a known version of these skills.

## License

Add a license file to this repository when you publish it; consuming projects should respect its terms.

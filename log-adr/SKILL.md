---
name: log-adr
description: >-
  Captures major engineering decisions from chat, drafts Architecture Decision
  Records (ADRs), and walks the user through approve/alter/skip/other before
  writing files under docs/adr/. Use when the user asks to log an ADR, record a
  technical decision, update the decision log, or after significant
  architecture, stack, security, or process choices in conversation.
---

# Log ADR (Architecture Decision Records)

You help the user maintain **ADRs** under **`docs/adr/`** in the **active project root** (not this skills repository unless that is the project open).

## Related layout (consuming repo)

```text
docs/adr/
  index.md              # Master table: date, deciders, one-liner, link
  0001-short-title.md   # One file per accepted decision
  0002-another-topic.md
```

## When to apply this skill

- The user invokes logging explicitly: “log ADR”, “record this decision”, “add to ADR”, `/log-adr`.
- The conversation **settles** a **non-trivial** technical choice worth remembering (see **Decision bar** below).

**Decision bar — propose an ADR when** several apply:

- Picks between real alternatives (tools, boundaries, data store, auth pattern).
- Affects multiple modules, teams, or future migrations.
- Hard to reverse or expensive to undo.
- Security, compliance, or operability implications.

**Skip proposing** for: trivial renames, obvious bugfixes, one-line lint fixes, or choices already recorded in an existing ADR (offer to **supersede** instead).

## Automatic capture (chat context)

1. **Track** candidate decisions as the chat progresses (mental draft: title + one sentence + why it matters).
2. Before writing files, **do not assume approval**. Always run the **Review dialog** below.
3. If multiple candidates accumulated, present **all** in one review round (numbered list).

If the user only wants a quick note without an ADR file, say so and offer a shorter path (e.g. issue comment only).

## Review dialog (mandatory before writing)

Present each candidate ADR in a compact block, then **stop and wait** for user input.

```
════════════════════════════════════════════════════════════
ADR REVIEW — Candidate #[n]
════════════════════════════════════════════════════════════
Title:    [proposed title]
Summary:  [one line for index.md]
Suggested file: docs/adr/[NNNN]-[kebab-title].md

Draft sections:
  Context:    [1–3 sentences]
  Decision:   [1–3 sentences]
  Consequences: [brief]

Reply with one of:
  APPROVE      — Write this ADR as drafted (Status → Accepted)
  ALTER        — Revise this draft using my feedback; re-show the block until satisfied
  SKIP         — Do not log this candidate (discard; no file). User can ask again later.
  OTHER        — Free text (see below)

════════════════════════════════════════════════════════════
```

### Option semantics

- **`ALTER`:** Same decision as the draft, different wording or emphasis. Loop: incorporate feedback → re-show block → user ends with **APPROVE**, **SKIP**, or **OTHER**.

- **`SKIP`:** Drops this candidate only. No persistence promise (“later” is the user starting a new chat or asking again). Covers what used to be split across *deny* and *defer*.

- **`OTHER` (required free-form):** User must supply intent in their own words. Use for anything that is not a small edit, including:
  - **Wrong decision captured:** User wants to log a *different* decision than the agent proposed — ask what actually was decided (or accept their paragraph), then **replace the draft entirely** and present a **new** review block (new title/summary/slug as needed) before **APPROVE**.
  - **Structural ops:** Merge two ADRs, split one into two, supersede `0003`, link prerequisites, etc. If superseding, set old file `Status: Superseded by [link]` when writing the new file.
  - **Out-of-band content:** User pastes the ADR body or bullet list they want on disk — normalize into the template, then re-show for **APPROVE** or **ALTER**.

After all candidates are resolved, summarize what will be written; on **APPROVE**, perform file operations.

## File naming and numbering

1. Resolve **project root** — the workspace root of the app being documented (where `docs/` should live).
2. Ensure **`docs/adr/`** exists (create if missing).
3. **Next number:** List `docs/adr/*.md` excluding `index.md`. Parse leading `\d{4}`; next is `max + 1`, padded to 4 digits (e.g. `0007`).
4. **Slug:** Lowercase kebab-case from title; strip punctuation; collapse spaces. Max ~50 chars for readability.
5. **Final path:** `docs/adr/{NNNN}-{slug}.md`

## ADR document body

Use the template in **`templates/adr-template.md`** in this skill directory. When writing into the **user’s repo**, fill placeholders:

- **Status:** `Accepted` once the user **APPROVE**s (use `Proposed` only if they ask to commit the text but decide status later).
- **Date:** ISO date `YYYY-MM-DD` (use the user’s “today” from context if available).
- **Deciders:** Prefer `git config user.name` (and optionally email) from the project; if unavailable, use the user’s display name from chat or `"Unspecified"`.

## Index file: `docs/adr/index.md`

Maintain a **single table** at the top (after an optional H1). **Newest first** (by date descending, then by ADR number descending).

### Table columns

| Column | Content |
|--------|---------|
| **Date** | `YYYY-MM-DD` |
| **Deciders** | Short string (names or team); comma-separated if several |
| **Summary** | **One line only** — no line breaks |
| **Detail** | Markdown link: `[title](./0001-slug.md)` |

### Index template (if `index.md` is missing)

```markdown
# Architecture Decision Records

Index of accepted technical decisions for this project. Newest entries first.

| Date | Deciders | Summary | Detail |
|------|----------|---------|--------|
```

### Updating the index

1. Read existing `docs/adr/index.md`.
2. Insert a **new row** below the header row of the table (newest first).
3. Preserve existing rows; fix only broken links if you rename files.
4. Do not duplicate the same ADR number.

## Execution checklist

- [ ] Confirmed user **APPROVE** (or equivalent) for each written ADR.
- [ ] Numbering does not collide with existing `NNNN-*.md`.
- [ ] `index.md` table has exactly **one line** of summary per ADR.
- [ ] Relative links in the index work from `docs/adr/`.

## After writing

Report:

```
✅ ADR logged
   Files: docs/adr/[NNNN]-[slug].md
          docs/adr/index.md (updated)
```

If **SKIP**, confirm nothing was written for that candidate.

## Conflicts and edge cases

- **Duplicate topic:** Offer to **supersede** the older ADR (update old file’s Status; new file references `Supersedes`).
- **Unclear project root:** Ask which workspace folder owns `docs/`.
- **Concurrent numbers:** Re-read directory immediately before creating a file; if a race is possible, suggest the user re-run once.

## What this skill does not do

- Replace `CLAUDE.md` or project standards — only **records decisions**; it does not auto-edit rules files.
- Run CI or tests — optional suggestion only.

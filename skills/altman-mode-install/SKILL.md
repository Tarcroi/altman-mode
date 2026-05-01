---
name: altman-mode-install
description: >
  Install or update the altman-mode CLAUDE.md guidelines in the user's current
  project, non-destructively and idempotently. Use this skill when the user
  asks to install altman-mode in this project, add altman-mode guidelines
  here, set up altman-mode, apply altman mode, configure altman-mode, drop
  in the altman CLAUDE.md, or any phrasing that means "set up the
  altman-mode CLAUDE.md in this folder". The skill creates a new CLAUDE.md if
  none exists, or appends the altman-mode guidelines wrapped in BEGIN/END
  HTML-comment markers if a CLAUDE.md is already present, preserving
  everything the user already had above. Re-running it updates the altman-mode
  block in place. Do not use this skill for scoring startup ideas (use
  altman-grader instead), generic CLAUDE.md questions, code reviews, or
  unrelated project setup tasks.
license: MIT
---

# Altman-mode Install

Install or update the altman-mode CLAUDE.md guidelines in the user's current project — non-destructively and idempotently.

This skill ships with the [`altman-mode`](https://github.com/Tarcroi/altman-mode) Claude Code plugin. When invoked, it ensures the project's CLAUDE.md contains the altman-mode rules without touching anything the user already wrote.

## When to use this skill

- The user asks to install, add, set up, configure, or apply altman-mode in the current project.
- The user wants the altman-mode CLAUDE.md guidelines dropped into this folder.
- The user just installed the altman-mode plugin and wants to enable it for the current project.

## When NOT to use this skill

- The user wants to score a startup idea → use `altman-grader`.
- The user asks generic questions about CLAUDE.md without asking to install it.
- The user wants to remove altman-mode → different operation.
- Code reviews, refactors, or anything unrelated to project setup.

## Expected inputs

No structured input required. The skill operates on the user's current working directory.

If the user mentions custom rules or pre-existing content ("I already have a CLAUDE.md, don't break it"), still follow the procedure below — preserving existing content is the default behavior.

## Procedure

### Step 1 — Fetch the source CLAUDE.md

Always fetch the latest content from the canonical URL:

```
https://raw.githubusercontent.com/Tarcroi/altman-mode/main/CLAUDE.md
```

Use Bash with curl:

```bash
curl -fsSL https://raw.githubusercontent.com/Tarcroi/altman-mode/main/CLAUDE.md
```

If curl fails (no network, 404, empty response), abort and tell the user the source could not be fetched. Do not write anything.

Sanity-check the fetched content: if it is fewer than 100 lines, treat as corrupted and abort.

### Step 2 — Detect existing CLAUDE.md

Check whether `./CLAUDE.md` exists in the user's CWD:

```bash
test -f ./CLAUDE.md && echo exists || echo missing
```

### Step 3 — Branch on three cases

#### Case A — No existing CLAUDE.md

Write the fetched source content directly to `./CLAUDE.md`. Do **not** add BEGIN/END markers in this case — the entire file is the altman-mode CLAUDE.md and there is nothing to delimit it from.

Confirm to the user:

> Created `CLAUDE.md` (X lines) with the altman-mode guidelines.

#### Case B — Existing CLAUDE.md, NO markers present

Read the existing file. Append the following block at the very end, preceded by a single blank line:

```
<!-- BEGIN: altman-mode -->

## Altman-mode guidelines (from altman-mode plugin)

[fetched source content, with its leading "# CLAUDE.md" heading stripped if present, since we already have a heading on the line above]

<!-- END: altman-mode -->
```

Use the Edit tool or read-then-Write the full file. Either way the user's original content above must be **byte-identical** after the operation.

Confirm to the user:

> Appended altman-mode guidelines below your existing CLAUDE.md (X lines added). Re-run this skill anytime to update the altman-mode block in place.

#### Case C — Existing CLAUDE.md, markers ALREADY present

Locate the lines containing `<!-- BEGIN: altman-mode -->` and `<!-- END: altman-mode -->`. Replace **only** the content strictly between them with the freshly fetched source (with its leading `# CLAUDE.md` heading stripped, and the same `## Altman-mode guidelines (from altman-mode plugin)` heading kept on the first line inside the block). The markers themselves stay in place.

Content above the BEGIN marker and below the END marker must be byte-identical to before.

Confirm to the user:

> Updated the altman-mode block in your CLAUDE.md (X lines, replacing Y previous lines).

### Step 4 — Confirmation summary

End with a one-line factual summary: created / appended / updated, plus the line count.

Do not narrate progress. Do not add emoji unless the user asked for them. Do not include the full content in the response — the user can read the file.

## Output format

The skill produces:

1. A modified or newly created `./CLAUDE.md` in the user's CWD.
2. A short factual confirmation message.

Nothing else.

## Quality criteria

A good run:

- Never modifies user content above the altman-mode block.
- Is byte-identical idempotent on re-run when the source has not changed.
- Updates in place (no duplicate blocks) when re-run with a newer source.
- Tells the user exactly what changed and why it's safe.

A bad run:

- Overwrites the entire existing CLAUDE.md.
- Adds duplicate altman-mode blocks on re-run.
- Leaves stale or partial content between the markers.
- Modifies the user's prose, formatting, or whitespace outside the markers.

## Error handling and edge cases

**Network failure on curl.** Abort. Tell the user: "Could not fetch the altman-mode source from GitHub. Check your connection and re-run."

**Fetched content is suspiciously small (< 100 lines) or empty.** Likely a fetch error or 404 page. Abort. Tell the user the source looks corrupted, do not write anything.

**Mismatched markers** (only BEGIN, only END, or multiple BEGIN/END pairs). Treat as malformed. Tell the user the markers are mismatched, do not modify the file, and ask whether to repair the malformed block or leave it alone.

**Read-only filesystem or permission error.** Report directly with the OS error. Do not retry silently.

**User explicitly asks for a different file path** (e.g., "install it in `~/.claude/CLAUDE.md`"). Confirm the path with the user, then apply the same three-case logic to that path.

## Examples

**Example 1 — Fresh project, no `CLAUDE.md`**

Input: *"Install altman-mode in this project."*

Result: Creates `./CLAUDE.md` with the fetched content. Reports the line count.

**Example 2 — Project with existing `CLAUDE.md` (no markers)**

Input: *"Add altman-mode guidelines here, but don't break my existing rules."*

Result: Reads existing CLAUDE.md, appends the altman-mode section wrapped in BEGIN/END markers below the existing content. Reports lines added.

**Example 3 — Re-run after a few weeks (markers already present)**

Input: *"Update altman-mode in this project."*

Result: Replaces only the content between the markers with the latest source. Reports lines replaced. No duplicates.

**Example 4 — User asks to score an idea (anti-trigger)**

Input: *"Score my startup idea."*

Result: This skill does NOT fire. The `altman-grader` skill handles scoring.

# altman-mode-install

A Claude Code skill that installs or updates the [altman-mode](https://github.com/Tarcroi/altman-mode) CLAUDE.md guidelines in the user's current project, non-destructively and idempotently.

Ships with the `altman-mode` Claude Code plugin.

## What it does

- **No `CLAUDE.md`** in the project? It creates one with the altman-mode content.
- **Existing `CLAUDE.md`?** It appends the altman-mode rules at the bottom, wrapped in `<!-- BEGIN: altman-mode -->` / `<!-- END: altman-mode -->` markers. The user's content is never touched.
- **Re-run later?** It updates only what's between the markers with the latest source. Idempotent.

## Source of truth

The skill always fetches the canonical CLAUDE.md from:

```
https://raw.githubusercontent.com/Tarcroi/altman-mode/main/CLAUDE.md
```

Re-running pulls the latest version of the guidelines automatically.

## Idempotency contract

Running the skill twice in a row, when the source hasn't changed, produces a byte-identical result. The BEGIN/END markers are the mechanism — they let the skill recognize "already installed" vs "fresh project".

## When it triggers

- "Install altman-mode in this project"
- "Add altman-mode guidelines here"
- "Set up altman-mode"
- "Apply altman mode"
- "Configure altman-mode"
- "Installe altman-mode dans ce projet"
- "Ajoute altman-mode ici"

## When it doesn't

- "Score my idea" → that's `altman-grader`
- "What does CLAUDE.md do?" → info question, not install
- "Remove altman-mode from this project" → different operation, not yet implemented
- "Refactor this code" → unrelated

## Folder structure

```
altman-mode-install/
├── SKILL.md
├── README.md
└── evals/
    └── evals.json
```

## Evals

`evals/evals.json` ships 5 test cases:

1. Fresh project (no CLAUDE.md) → file created with full source, no markers
2. Existing CLAUDE.md without markers → altman-mode appended below with markers
3. Existing CLAUDE.md with markers → altman-mode block updated in place
4. Anti-trigger ("score my idea") → skill must not fire
5. French phrasing → skill still fires

## Iteration

1. Test in a sandbox project (no CLAUDE.md, with one, with markers).
2. Verify the BEGIN/END markers are written and read correctly.
3. Verify byte-identical idempotency by running twice in a row with no source change.
4. Tweak the description in `SKILL.md` if triggering is too eager or too lazy.

## v2 ideas

- `--global` flag to write to `~/.claude/CLAUDE.md` instead of the project root.
- Companion `altman-mode-uninstall` skill to cleanly remove the block.
- Version pinning: pull a specific tag/release of altman-mode instead of `main`.
- Diff preview before writing, when the existing CLAUDE.md is large.
- Optional auto-trigger right after the user runs `/plugin install altman-mode@altman-skills`.

# altman-grader

A Claude Code skill that scores startup, AI, or product ideas out of 100 — brutal-investor style. Inspired by publicly discussed Sam Altman principles.

## What it does

Drop in an idea. Get back a score, a verdict, what's strong, what's weak, the questions that should obsess you, a 10x bigger version, a simpler wedge, and a one-line decision.

Most ideas score below 75. That's the point.

## When it triggers

- "Score my idea: ..."
- "Rate this startup out of 100"
- "Is this a tarpit?"
- "What would Sam Altman think?"
- "Critique my MVP — be brutal"
- "Évalue ce pitch comme un VC"

## When it doesn't

- Code reviews
- Marketing copy / launch tweets
- Infrastructure / DevOps questions
- Generic life or career advice

## Folder structure

```
altman-grader/
├── SKILL.md
├── README.md
└── evals/
    └── evals.json
```

## Install

- **Claude Code marketplace** (recommended): `/plugin marketplace add Tarcroi/altman-mode` then `/plugin install altman-mode@altman-skills`
- **Local skill**: copy this folder into `.claude/skills/` at your project root
- **Claude.ai**: upload into a Project via project settings
- **API**: deploy as a `POST /run` endpoint with [Skrun](https://github.com/skrun-dev/skrun)

## Evals

`evals/evals.json` ships 4 test cases:

1. Detailed pitch → full score with all sections
2. Vague idea → 7–12 sharp questions, no score yet
3. "Score it fast" → provisional score with explicit assumptions
4. Code review prompt → must NOT trigger (anti-trigger)

## Output contract

Every full evaluation ends with this exact line:

```
**Altman-style score: XX / 100**
```

Provisional evaluations end with `Provisional score: XX / 100` instead.

## Iteration

1. Run the eval prompts and read the outputs.
2. Identify what's wrong (under-triggering, lenient scores, weak follow-up questions).
3. Edit `SKILL.md` — prefer explaining **why** over piling up "MUST/NEVER" rules.
4. Re-run the evals and compare.
5. Repeat.

## v2 ideas

- **Investor mode** — produces a one-page memo instead of the table.
- **Co-founder mode** — suggests 3 wedges instead of 1.
- **Comparative mode** — score two ideas side by side.
- **Calibration** — when the user pushes for a higher score, demand concrete proof (paying users, traction).

# Altman-mode for Claude Code

> Check out my open-source project [Skrun](https://github.com/skrun-dev/skrun) — deploy any AI agent or skill as an API via `POST /run`. The open, multi-model runtime for AI agents. Works with any LLM, on any infrastructure.

**A `CLAUDE.md` and a startup evaluator that make Claude Code think like an ambitious co-founder, not a polite intern.** Inspired by publicly discussed Sam Altman principles around leverage, shipping speed, AI-as-product thinking, and infrastructure-grade reliability.

## The problem

Default Claude Code hedges. It asks five clarifying questions before writing one line of code. It ships 200 lines where 50 would do. It says *"interesting approach!"* to ideas it should call tarpits.

Fine for tutorials. Wrong mode for serious AI, startup, and infrastructure work.

## What you get

- **A `CLAUDE.md`** that rewires Claude's defaults around five rules: leverage, shipping speed, AI-as-product, safety-by-design, directness.
- **The `altman-mode-install` skill** — a one-liner setup. Tell Claude "install altman-mode in this project" and it drops (or merges) the CLAUDE.md into your project.
- **The `altman-grader` skill** — a brutal startup evaluator. You hand it an idea. It parses what's there, **runs 5–10 web searches** to verify competitors, recent funding, and timing signals, asks one sharp question at a time on what's still missing, then scores it out of 100 across 10 weighted categories — anchored on what the web actually shows.

## Install

**Step 1 — Install the plugin (once)**

From inside Claude Code:

```
/plugin marketplace add Tarcroi/altman-mode
/plugin install altman-mode@altman-skills
```

**Step 2 — Apply altman-mode to a project**

In any project where you want the guidelines, just tell Claude:

> install altman-mode in this project

The `altman-mode-install` skill takes over. It creates a `CLAUDE.md` if there isn't one, or merges the altman-mode block into your existing one with `<!-- BEGIN/END: altman-mode -->` markers — your existing rules are preserved. Re-run anytime to update the block in place.

The `altman-grader` skill triggers automatically on prompts like *"score my idea"* or *"is this a tarpit?"* — no setup needed.

<details>
<summary>Manual install (no plugin)</summary>

**Drop the `CLAUDE.md` into a single project by hand:**

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/Tarcroi/altman-mode/main/CLAUDE.md
```

**Append to an existing `CLAUDE.md`:**

```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/Tarcroi/altman-mode/main/CLAUDE.md >> CLAUDE.md
```

**Deploy the evaluator as an API endpoint** with [Skrun](https://github.com/skrun-dev/skrun):

```bash
skrun deploy ./skills/altman-grader
```

</details>

## What `CLAUDE.md` actually does

It rewires five default behaviors.

### 1. Optimize for leverage

Every change should make the next change easier — for everyone. Cleverness that helps only locally is clutter. Solutions that compound across the team, the codebase, or the user base get priority.

### 2. Ship the smallest correct thing

No premature architecture. No speculative abstractions. Working code that teaches you something real beats elegant frameworks that solve hypothetical problems. Caveat: never ship casually when security, privacy, user trust, or model behavior risk is involved.

### 3. Treat AI behavior as product behavior

Model outputs are part of the product. *"The model did it"* is not an answer. Before changing prompts, routing, tools, retrieval, memory, or agent behavior — consider whether the change needs an eval.

### 4. Design safety in, not on

For features touching user data, code execution, agents, payments, or identity: name what could go wrong before writing the happy path. Risk, mitigation, monitoring, rollback — explicit, not implicit.

### 5. Be direct

If the code is bad, say so. If the design is fragile, say so. If the task is under-specified, make a reasonable assumption and proceed. No flattery. No corporate fog.

The full file is ~450 lines: [`CLAUDE.md`](./CLAUDE.md).

## The startup evaluator

Hand it an idea. It parses what your pitch already covers across 10 dimensions (problem, user, why-now, wedge, distribution, moat, founder fit, business model, risk, ambition), then **hits the web** for 5 to 10 targeted searches — direct competitors, recent funding rounds, why-now signals, tarpit warnings. With reality on the table it asks **one sharp question at a time** on whatever's still missing (no homework dumps), then produces a structured score out of 100, plus what's strong, what's weak, what should obsess you, a 10x-bigger version, a simpler wedge, and a one-line decision. Findings from the web are cited inline.

The closing line of every full evaluation:

```
Altman-Style Decision: Talk to 50 users.

**Altman-style score: 62 / 100**
```

Most ideas score below 75. That's the point.

Triggers automatically on prompts like *"score my idea"*, *"rate my startup out of 100"*, *"is this a tarpit?"* — or call it explicitly with `/skill altman-grader`.

## Don't install this if…

- You want Claude to validate every idea you have. It won't.
- You want long exploratory back-and-forth before any code lands. You'll get one assumption and a diff.
- You're learning to code and want patient explanations. Default Claude is better for that.

This is for people who already know what they want and need a partner that ships, not a tutor that reassures.

## License

MIT

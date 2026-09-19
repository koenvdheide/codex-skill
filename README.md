# codex-skill

A [Claude Code](https://claude.ai/code) plugin that invokes the local [Codex CLI](https://github.com/openai/codex) as an independent analysis partner.

## What it does

Gives Claude Code a structured way to delegate analysis tasks to Codex: brainstorming alternatives, red-teaming plans, debugging from a fresh perspective, reviewing diffs adversarially, and more. Codex runs locally, reads the codebase, and returns its analysis for Claude to synthesise.

## Supported modes

Brainstorm, Red-team, Debug, Plan Review, Diff Review, Spec Extraction, Rollout/Rollback, Compare/Decide, Test Gaps, Explain, Post-mortem, Attack Surface, Exhausted Hypotheses.

## Does it actually help?

Across ~450 Codex reviews in my own Claude Code transcripts, ~32% pushed past a local edit into a plan or direction change. Weakest on subjective style review. The full measurements, the method behind them and the caveats live in the [agent-tools README](https://github.com/koenvdheide/agent-tools#what-codex-reviews-add).

## Convergence mode (iterative review)

Codex can run in a convergence loop for artifacts that evolve across multiple revisions (specs, plans, designs): review → fix → re-review until the reviewer gives an affirmative verdict or you stop. Claude orchestrates the loop with user gates after each round, and cites prior findings on each pass so the reviewer can detect drift. It also watches for the scope-drift failure mode, where each round's "real" findings pull the artifact into a design the user never asked for.

See the Convergence Mode section in `skills/codex/SKILL.md` for the loop shape (two user decisions per round: which fixes to apply, then whether to continue), per-round prompt construction, and the anti-pattern guidance on when Claude should stop and re-confirm scope.

## Prerequisites

- [Claude Code](https://claude.ai/code)
- [Codex CLI](https://github.com/openai/codex) installed and on PATH
- Bash or Git Bash (the skill's recipes use heredocs, pipes and `cygpath`)

## Summary QA

After summarising a high-stakes mode (`plan-review`, `red-team`, `diff-review`, `exhausted-hypotheses`, `attack-surface`) the skill re-reads its own summary against the fidelity rules before presenting it: every evaluative verb quoted verbatim, inline citations counted in prose as well as bullets.

## Installation

Via the `agent-tools` marketplace:

```text
/plugin marketplace add koenvdheide/agent-tools
/plugin install codex@agent-tools
/reload-plugins
```

Refresh later with `/plugin marketplace update agent-tools`, then `/plugin update codex@agent-tools` and `/reload-plugins`.

## Usage

Claude invokes the skill automatically when a task matches, or you can invoke it directly:

```text
/codex:codex red-team my authentication refactor plan
```

## License

MIT

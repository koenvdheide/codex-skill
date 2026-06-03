# codex-skill

A [Claude Code](https://claude.ai/code) plugin that invokes the local [Codex CLI](https://github.com/openai/codex) as an independent analysis partner.

## What it does

Gives Claude Code a structured way to delegate analysis tasks to Codex — brainstorming alternatives, red-teaming plans, debugging from a fresh perspective, reviewing diffs adversarially, and more. Codex runs locally, reads the codebase, and returns its analysis for Claude to synthesize.

## Supported modes

Brainstorm, Red-team, Debug, Plan Review, Diff Review, Spec Extraction, Rollout/Rollback, Compare/Decide, Test Gaps, Explain, Post-mortem, Attack Surface, Exhausted Hypotheses.

## Does it actually help?

Across my own Claude Code transcripts (one operator, so an internal audit, not a benchmark): ~450 Codex reviews over 9 projects, ~436 judged in full.

Across that set: ~3 findings each, a ~2.1% false-finding rate, and ~32% that pushed past a local edit into a plan or direction change. In convergence mode (below), ~62% of review chains reached an affirmative verdict in-session. Weakest on subjective style review. Full breakdown and caveats: the [agent-tools README](https://github.com/koenvdheide/agent-tools#what-codex-reviews-add).

## Session resume

Multi-round iterations on the same artifact (review plan v1 → v2, iterative debugging) can preserve prior Codex context via named sessions:

```text
/codex --new-session review-auth   # create a named session
/codex --session review-auth       # resume it later
/codex list                        # see all sessions
/codex delete review-auth          # clean up
```

Sessions are stored per-worktree under `.claude/.codex-sessions/`. See the Session Management section in `skills/codex/SKILL.md` for the full workflow.

## Convergence mode (iterative review)

For reviewing artifacts that evolve across multiple revisions — specs, plans, designs — Codex can run in a convergence loop: review → fix → re-review until the reviewer gives an affirmative verdict or you stop. Claude orchestrates the loop with user gates after each round, cites prior findings on each pass so the reviewer can detect drift, and watches for the scope-drift failure mode where each round's "real" findings pull the artifact into a design the user never asked for.

See the Convergence Mode section in `skills/codex/SKILL.md` for the loop shape (two user decisions per round — which fixes to apply, then whether to continue), per-round prompt construction, and the anti-pattern guidance that tells Claude when to stop and re-confirm scope instead of continuing.

## Prerequisites

- [Claude Code](https://claude.ai/code)
- [Codex CLI](https://github.com/openai/codex) installed and on PATH
- Bash or Git Bash (the skill's session management uses `source` on a bash script — `skills/codex/session-mgr.sh`)

## Optional: `reviewer` subagent

The skill runs a mandatory QA pass using a `reviewer` subagent after summarizing high-stakes modes (`plan-review`, `red-team`, `diff-review`, `exhausted-hypotheses`, `attack-surface`). It expects the subagent from [koenvdheide/claude-reviewer](https://github.com/koenvdheide/claude-reviewer). Without it, the skill falls back to self-review against the same fidelity rules — workflow still works, just less rigorous.

## Installation

Via the `agent-tools` marketplace:

```text
/plugin marketplace add koenvdheide/agent-tools
/plugin install codex@agent-tools
/reload-plugins
```

Refresh later with `/plugin marketplace update agent-tools`, then `/reload-plugins`.

## Usage

Claude invokes the skill automatically when a task matches, or you can invoke it directly:

```text
/codex red-team my authentication refactor plan
```

## License

MIT

# codex-skill

A [Claude Code](https://claude.ai/code) skill that invokes the local [Codex CLI](https://github.com/openai/codex) as an independent analysis partner.

## What it does

Gives Claude Code a structured way to delegate analysis tasks to Codex — brainstorming alternatives, red-teaming plans, debugging from a fresh perspective, reviewing diffs adversarially, and more. Codex runs locally, reads the codebase, and returns its analysis for Claude to synthesize.

## Supported modes

Brainstorm, Red-team, Debug, Plan Review, Diff Review, Spec Extraction, Rollout/Rollback, Compare/Decide, Test Gaps, Explain, Post-mortem, Attack Surface, Exhausted Hypotheses.

## Prerequisites

- [Claude Code](https://claude.ai/code)
- [Codex CLI](https://github.com/openai/codex) installed and on PATH

## Installation

Copy or clone into your Claude Code skills directory:

```bash
git clone https://github.com/koenvdheide/codex-skill.git ~/.claude/skills/codex
```

## Usage

Claude invokes the skill automatically when a task matches, or you can invoke it directly:

```
/codex red-team my authentication refactor plan
```

## License

MIT

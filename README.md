# ai-rules

A collection of the AI-related material I use in my work — instruction files,
prompts, skills, and related artifacts — shared publicly so others can read,
borrow, and adapt them.

The repo is organized **by artifact type**. As the collection grows, new kinds
of material get their own top-level folder (e.g. `skills/`, `prompts/`).

## Contents

### `claude-md/` — project instruction files (`CLAUDE.md`)

Working `CLAUDE.md` files that guide Claude when authoring real projects. These
are not idealized examples written for show; they are the actual files in use.
They contain project-specific references (TODOs, open items, collaboration
protocol) precisely because that's what a real working instruction file looks
like.

The guides are **layered**: [`claude-md/tutorials/CLAUDE.md`](claude-md/tutorials/CLAUDE.md)
is the general guide for authoring "normal" data science tutorials, and the
project guides build on it rather than repeating it.

| File | Scope | What it covers |
|------|-------|----------------|
| [`claude-md/tutorials/CLAUDE.md`](claude-md/tutorials/CLAUDE.md) | General — any `learnr` data science tutorial | The base authoring guide for "normal" (post-infrastructure) tutorials: the AI-era philosophy, tutorial structure, the render + Live Server workflow, exercise rhythm, question types, knowledge drops, code-chunk conventions, the setup chunk, data handling, and formatting. Originated in [vscode.tutorials](https://github.com/PPBDS/vscode.tutorials); not specific to it. The package's own infrastructure tutorials (`01-code`–`09-infrastructure`) are the explicit exception that this guide does *not* govern. |
| [`claude-md/primer/CLAUDE.md`](claude-md/primer/CLAUDE.md) | [Preceptor's Primer](https://github.com/PPBDS/primer) | Builds on the general tutorial guide above and covers only what is specific to the *Preceptor's Primer for Bayesian Data Science* textbook and its matching tutorials: curriculum shape (Cardinal Virtues, EMH tiers, predictive/causal alternation), chapter vs. tutorial structure, Preceptor and Population Tables, a knowledge-drop library, a master exercise list, per-tutorial seed specs, and an LLM regeneration strategy. |

These guides come from projects under the [PPBDS](https://github.com/PPBDS)
GitHub organization:

- **vscode.tutorials** — package site: <https://ppbds.github.io/vscode.tutorials/>
- **Preceptor's Primer** — book: <https://ppbds.github.io/primer/> · tutorials: <https://ppbds.github.io/primer/tutorials/>

## License

[MIT](LICENSE).

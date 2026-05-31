# ai-rules

A collection of the AI-related material I use in my work — instruction files,
prompts, skills, and related artifacts — shared publicly so others can read,
borrow, and adapt them.

The repo is organized **by artifact type**. As the collection grows, new kinds
of material get their own top-level folder (e.g. `skills/`, `prompts/`).

## Contents

### `claude-md/` — project instruction files (`CLAUDE.md`)

Working `CLAUDE.md` files that guide Claude when authoring real projects. These
are not idealized examples written for show; they are the actual files in use,
copied here verbatim. They contain project-specific references (TODOs, open
items, collaboration protocol) precisely because that's what a real working
instruction file looks like.

| File | Project | What it covers |
|------|---------|----------------|
| [`claude-md/vscode-tutorials/CLAUDE.md`](claude-md/vscode-tutorials/CLAUDE.md) | [vscode.tutorials](https://github.com/PPBDS/vscode.tutorials) | Authoring guide for an R package of `learnr` tutorials that teach VS Code + AI-driven data science in GitHub Codespaces. Covers tutorial structure, the render + Live Server workflow, exercise rhythm, knowledge drops, and code-chunk conventions. (~300 lines) |
| [`claude-md/primer/CLAUDE.md`](claude-md/primer/CLAUDE.md) | [Preceptor's Primer](https://github.com/PPBDS/primer) | The full authoring reference for the *Preceptor's Primer for Bayesian Data Science* textbook and its matching tutorials, organized around the Cardinal Virtues. A 17-section spec covering curriculum shape, chapter/tutorial structure, a knowledge-drop library, a master exercise list, per-tutorial seed specs, and an LLM regeneration strategy. (~3,100 lines) |

Both projects live under the [PPBDS](https://github.com/PPBDS) GitHub
organization:

- **vscode.tutorials** — package site: <https://ppbds.github.io/vscode.tutorials/>
- **Preceptor's Primer** — book: <https://ppbds.github.io/primer/> · tutorials: <https://ppbds.github.io/primer/tutorials/>

## License

[MIT](LICENSE).

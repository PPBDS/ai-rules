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
[`claude-md/CLAUDE.md`](claude-md/CLAUDE.md) is the **overview/map** that explains
which of the three guides applies to which package — start there.

| File | Scope | What it covers |
|------|-------|----------------|
| [`claude-md/tutorials/CLAUDE.md`](claude-md/tutorials/CLAUDE.md) | General — any `learnr` data science tutorial | The base authoring guide for "normal" (post-infrastructure) tutorials: the AI-era philosophy, tutorial structure, the render + Live Server workflow, exercise rhythm, question types, knowledge drops, code-chunk conventions, the setup chunk, data handling, and formatting. Originated in [vscode.tutorials](https://github.com/PPBDS/vscode.tutorials); not specific to it. It applies to every tutorial **except** those in the `vscode.tutorials` and `tutorial.helpers` packages (which teach the tools and mechanics themselves); `misc.tutorials`, the Primer, and any future package follow it by default. |

**The other two guides live in their own repos, not here** — each is specific to one project and builds on the base guide above:

- **`vscode.tutorials`** — the infrastructure / mechanics tutorials that teach the tools the base guide *assumes* (the exception it does not govern): <https://github.com/PPBDS/vscode.tutorials/blob/main/CLAUDE.md>
- **Preceptor's Primer** — its chapters (`book/`) and tutorials (`primer.tutorials/`), as a root index plus `guide/` parts read on demand: <https://github.com/PPBDS/primer/blob/main/CLAUDE.md>

Only the shared **base tutorial guide** (the table above) lives in this repo; the [overview map](claude-md/CLAUDE.md) routes to all three.

These guides come from projects under the [PPBDS](https://github.com/PPBDS)
GitHub organization:

- **vscode.tutorials** — package site: <https://ppbds.github.io/vscode.tutorials/>
- **Preceptor's Primer** — book: <https://ppbds.github.io/primer/> · tutorials: <https://ppbds.github.io/primer/tutorials/>

## License

[MIT](LICENSE).

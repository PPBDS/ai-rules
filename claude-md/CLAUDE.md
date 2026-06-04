# CLAUDE.md — Tutorial authoring guides: overview & map

This directory holds the authoring guides for the PPBDS learnr-tutorial ecosystem.
There are three, and **which one applies depends entirely on which package / repo you
are working in.** Read this file first to route yourself to the right document, then
follow that document.

## The three documents

| Document | Governs | Use it when you are authoring… |
|---|---|---|
| [`vscode/CLAUDE.md`](vscode/CLAUDE.md) | The **`vscode.tutorials`** package — the introductory tutorials students start with, which teach the tools and environment (Git, GitHub, `.gitignore`, terminals, Codespaces, Quarto rendering, CP/CR, QMD World vs R World, package ecosystems). | a tutorial in `vscode.tutorials`. |
| [`tutorials/CLAUDE.md`](tutorials/CLAUDE.md) | **Every other tutorial** — the default contract for "normal," post-infrastructure data science tutorials (`misc.tutorials`, the Primer's tutorials, any future package). | a normal tutorial that is **not** in `vscode.tutorials` (or `tutorial.helpers`). |
| [`primer/CLAUDE.md`](primer/CLAUDE.md) | The **`primer` repo** — both its book chapters (`book/`) and its tutorials (`primer.tutorials/`), organized around the Cardinal Virtues. | anything in the `primer` repo. |

## How they relate

1. **`vscode.tutorials` stands alone.** It is the first thing students do, and it
   *teaches* the mechanics every later tutorial takes for granted. **Nothing in the
   other two documents applies to it, and nothing in it applies to them.**
   (`tutorial.helpers`' own tutorials are the one other "teaches the tools" package and
   sit outside the base guide for the same reason.)

2. **`tutorials/CLAUDE.md` is the default for everything else.** Every tutorial outside
   `vscode.tutorials` / `tutorial.helpers` follows it — *because they all assume the
   student has already completed `vscode.tutorials`.* That shared assumption (students
   know Git, terminals, rendering, CP/CR) is exactly what lets the base guide skip the
   mechanics and focus on data science. A project may depart from a base-guide rule only
   by an **explicit, on-the-record override** in its own guide; silent divergence is a
   bug.

3. **`primer/CLAUDE.md` builds on the base guide.** The Primer's tutorials are normal
   tutorials, so the Primer guide **reads in and accepts `tutorials/CLAUDE.md` first**,
   then adds only what is specific to the Primer (the Cardinal Virtues structure, the
   Easy/Medium/Hard progression, Preceptor and Population Tables, the master exercise
   list) and records any deliberate overrides. The Primer guide **also** covers the
   **book chapters** in `book/`, which are prose Quarto — not learnr tutorials — and are
   therefore outside the base guide's scope entirely.

## Which document do I follow?

- Working in **`vscode.tutorials`** → [`vscode/CLAUDE.md`](vscode/CLAUDE.md). Stop there;
  the others do not apply.
- Working in the **`primer` repo** — a `book/` chapter or a `primer.tutorials/` tutorial
  → [`primer/CLAUDE.md`](primer/CLAUDE.md), which itself routes you to
  [`tutorials/CLAUDE.md`](tutorials/CLAUDE.md) for the shared tutorial rules.
- Working in **`misc.tutorials`**, or any other normal tutorial package →
  [`tutorials/CLAUDE.md`](tutorials/CLAUDE.md).
- Working in **`tutorial.helpers`'** own tutorials → like `vscode.tutorials`, the base
  guide does not apply; teach the mechanics directly.

(The repo-root [`README.md`](../README.md) describes the whole `ai-rules` collection;
*this* file is specifically the map for the tutorial-authoring guides under `claude-md/`.)

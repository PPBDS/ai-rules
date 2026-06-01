# CLAUDE.md — vscode.tutorials

`vscode.tutorials` is the **infrastructure / mechanics** tutorial package: the first
tutorials a student does, which teach the *tools and environment* — Git, GitHub,
`.gitignore`, `_files` directories, terminals, GitHub Codespaces, Quarto rendering,
the distinction between QMD World and R World, package ecosystems, CP/CR, and the
rest of the working setup.

**This package is the explicit exception to the base tutorial guide.** The base
guide ([`claude-md/tutorials/CLAUDE.md`](../tutorials/CLAUDE.md)) is the default
contract for *normal*, post-infrastructure data science tutorials, and it explicitly
does **not** govern this package. Everything the base guide tells a normal tutorial to
*assume* — that students already know Git, terminals, rendering, and CP/CR — is what
the tutorials here exist to *teach*. So the base guide's rules (no exercise code
chunks, no generic knowledge drops, work-in-the-QMD-not-the-R-Terminal, and so on) do
not bind these tutorials. Teach the mechanics directly.

(The `tutorial.helpers` package's tutorials are the other base-guide exception, for
the same reason.)

> This file is a starting point. It currently does little more than house the
> infrastructure knowledge drops salvaged from the normal tutorials (below). A fuller
> authoring guide for `vscode.tutorials` can grow here over time.

## Generic knowledge drops to integrate

As the normal tutorials (the Primer; `misc.tutorials`) adopted the base guide's
knowledge-drop rule — every drop must (1) make a key point from the companion
chapter, (2) talk about the data, or (3) comment on what the most recent command
displayed — a set of **generic infrastructure knowledge drops** were pulled out of
them. Those lessons are real and worth teaching; they just belong *here*, in the
infrastructure tutorials, not in a data science tutorial.

They are recorded below so nothing is lost. **TODO: do a better job of including these
within `vscode.tutorials`** — as progressive, spaced lessons woven into the relevant
exercises, rather than the canned one-liners and template ladders they started as.

### Working in the cloud
> Professionals keep their data science work in the cloud because laptops fail.

### Spaced repetition
> The best way to ensure that students remember these concepts more than a few months
> after the course ends is spaced repetition, although we focus more on the repetition
> than on the spacing.

### QMD World vs R World
The render runs in its own R session; the R Terminal is a different session; a library
or object set up in one is not present in the other. A ladder of increasing
sophistication (salvaged from the retired Primer §12.6 Theme 1):

1. The two worlds exist. Rendering runs in its own R session; the R Terminal is a
   different session. A library or object you set up in one is not present in the
   other.
2. Rendering runs in a *fresh* session — packages loaded in the R Terminal are
   invisible to the render, which is why every library the document needs must be in
   the QMD's setup chunk.
3. Several R sessions can run simultaneously (R Terminal, render, AI agent), each with
   its own workspace.
4. The isolation is usually a feature: renders start from a known-clean state, so
   results don't depend on whatever is loaded in an interactive session.
5. The rare failure mode is when the sessions *do* share state — writing to the same
   file, reading a cache another process is updating — and those are almost always
   bugs.
6. In modern workflows neither is a single instance: many R sessions run in parallel
   (shared Codespaces, `Rscript` jobs, AI agents spawning their own sessions).
   Parallelism is the norm; non-interaction is what makes it work; when they do
   interact, expect trouble.

### `library(tidyverse)` and package ecosystems
A ladder from "what the tidyverse is" through "why conflicts matter" to "what a
namespace is" (salvaged from the retired Primer §12.6 Theme 2):

1. The tidyverse is a family of packages that share a common design philosophy and
   grammar — **dplyr** for manipulation, **ggplot2** for plotting, **readr** for I/O,
   and several others. `library(tidyverse)` loads the core set at once.
2. The attach message ends with a "Conflicts" section naming functions that exist in
   more than one package — `dplyr::filter()` masks `stats::filter()`. The last-loaded
   package wins, so after `library(tidyverse)` the `filter()` you get is dplyr's.
3. Why masking matters: `filter()` from dplyr behaves very differently from `filter()`
   in base R. The masking is deliberate — the tidyverse is saying "our version is what
   you want."
4. **Namespaces**: every function in R lives in a package's namespace. `dplyr::filter`
   names the function explicitly and avoids masking entirely. In reusable code
   (packages, sourced scripts), reach for the namespace prefix rather than relying on
   load order.

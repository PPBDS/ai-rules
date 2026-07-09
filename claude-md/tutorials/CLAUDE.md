# CLAUDE.md — Tutorial authoring guide

This is the authoring guide for **"normal" tutorials**: the data science tutorials students do *after* they have learned the basic mechanics of their tools. **Read §1–§3 before writing anything** — the contract, the working model, and the shape of a single exercise are most of what you need. §4–§7 are reference.

## Contents

1. **Scope and contract** — which tutorials this governs, the exceptions, the override protocol.
2. **The model** — what a tutorial is for, and how students work (QMD + render + Live Server; one working chunk; almost never the R Terminal; caching).
3. **The shape of one exercise** — the canonical question pattern, the `echo = TRUE` answer, the knowledge drop; question types.
4. **Structure of a whole tutorial** — Introduction / topic / Summary sequences, the sequencing guard, commits, the analysis path.
5. **Writing rules** — prompts to AI, knowledge drops, submission evidence, formatting.
6. **Mechanics and checking** — chunk conventions, setup chunk, test-chunk length, data handling, images, verbatim display, checking, DESCRIPTION.
7. **Choosing topics** — the one project-specific part; points to your project's guide.

---

## 1. Scope and contract

This guide is not specific to any one package. The same guidance applies to any **learnr** tutorial that teaches a data science topic to students who already know how to work in their environment — across `misc.tutorials`, the Primer, and any other tutorial package in this ecosystem.

**This guide is the default contract for every such tutorial.** Unless a tutorial is one of the explicit exceptions below, it follows everything here by default. A project may override a rule — but the override must be **explicit**: stated in that project's own guide, named as a departure from this base, and justified. Silent divergence is a bug. Project guides (e.g. the Primer's) read in and accept this file first, then add only what is specific to them or override it on the record.

**Assumed skills.** These tutorials assume students already understand the foundational skills: Git, GitHub, `.gitignore`, `_files` directories, terminals, Codespaces, and Quarto rendering. Tutorials written under this guide do **not** re-teach those mechanics.

**The exception is two whole packages, not a list of tutorials.** This guide does **not** apply to any tutorial in the `vscode.tutorials` package or the `tutorial.helpers` package. Those packages teach the tools and mechanics themselves — Git, GitHub, Codespaces, Quarto, the `tutorial.helpers` exercise system — which is precisely what this guide assumes students have already done. Every *other* tutorial follows this guide by default: `misc.tutorials`, the Primer, and any future package. There is no need to enumerate which tutorials are in or out — membership in `vscode.tutorials` or `tutorial.helpers` is the entire exception.

**This guide governs tutorials only.** Other artifacts a project may produce (textbook chapters, classroom exercises, slide decks) are **out of scope** here; each such artifact defines its own relationship to this guide. The Primer, for example, applies this guide to its tutorials but not to its prose book chapters.

Almost everything below is **universal** — true of any normal tutorial regardless of package. The one genuinely package-specific concern is **how a tutorial's topic is chosen** (see §7, *Choosing topics*): each project's own guide defines its topic model, so this guide only points there. A few rules state a **default value or cadence** a project may override on the record (per the contract above); those are noted inline.

**Project layout.** Tutorials are **learnr** tutorials shipped inside an R package (e.g. `misc.tutorials`, `primer.tutorials`), living in `inst/tutorials/<name>/` as `tutorial.Rmd` files:

```
inst/tutorials/<name>/
  tutorial.Rmd       # main tutorial file
  data/              # source copies of the data students download (read as data/<file>)
  images/            # images included via include_graphics()
```

---

## 2. The model

### Purpose: practice, not the artifact

**The purpose is practice, not the artifact.** A tutorial does not exist to help the student produce a particular plot or analysis — if it did, we would just hand her our code. It exists so she can **practice using AI to achieve well-specified goals**: stating what she wants, judging what the AI hands back, noticing when it is wrong, and refining. Copy-pasting our code skips the very skill we are teaching. This is the root reason we describe goals rather than paste code, and show our code only *afterward* — as something to compare against, never something to copy.

The goal is not to teach coding — it is to teach students how to **use AI to create data science artifacts**. Once students are past the infrastructure tutorials, they should mostly interact with an AI agent that edits their files, renders their Quarto document, and helps them debug. They should learn to steer analysis, inspect output, notice problems, and ask for refinements.

- These are data science tutorials. They should follow an exploratory path through data toward a useful artifact (usually a published page or website, but sometimes just a polished local document).
- Show students **results** (plots, printed tibbles, summary statistics, rendered pages), not just the code that produced them.
- Questions ask students to prompt AI for file edits, render, inspect output, and compare their output to ours.
- Prefer many small exercises that form a data analysis path over one large prompt that solves the whole section.

### Student workflow: render + Live Server

Students view all QMD output via **render + Live Server**, not by running code in the R Terminal:

- After editing `analysis.qmd`, students run `quarto render analysis.qmd` in a bash Terminal.
- They open `analysis.html` with Live Server once at the start (right-click in Explorer → "Open with Live Server"); it auto-refreshes on every subsequent render.
- **"Render" is the shorthand.** Once the Introduction has set up Live Server, later exercises just say **"Render."** — not *"in a bash Terminal, run `quarto render analysis.qmd` and check your Live Server tab"* spelled out every time. The Introduction establishes the shorthand explicitly (see *Introduction exercises*); the one place you still spell out `quarto render analysis.qmd` is that first setup render, before the shorthand is introduced.
- **Never** instruct students to use `Cmd/Ctrl + Enter` to run QMD code or `Cmd/Ctrl + Shift + K` to render.

### One evolving working chunk per topic

Within a topic, students do *not* accumulate a new code chunk per exercise. They keep a single **working chunk** and evolve it: it first prints the raw data, then prints a few columns, then a rough plot, then a polished plot — each exercise *replaces* the code that was there before. That is fine, because only the final state matters. A topic therefore ends with one chunk holding its final graphic (or table); starting the next topic starts a fresh working chunk. So a finished `analysis.qmd` is short — the setup chunk plus roughly one chunk per topic (a two-topic tutorial ends with three chunks: setup, topic-one graphic, topic-two graphic). The main exception is a **separate data chunk**: a download or a slow data-preparation step (an API pull, reading and cleaning a file) earns its own chunk — often cached — so it is not re-run as the working chunk evolves. Word prompts lightly. The student is *always* working in the last (working) chunk, so usually you need not mention the chunk at all — just state the goal, and the working chunk is understood. Only call out a chunk explicitly when you genuinely want the student to **save the current chunk and start a new one** (for example, right after caching a data chunk), which is rare. And never write *"replace the code … with a new code chunk"* — code is replaced with different *code*, not with a chunk. (This is also why the expected-output block we show almost always reflects the most recent state of that one chunk.)

### Almost everything happens in the QMD, not the R Terminal

Students do their work by prompting the AI agent to edit `analysis.qmd`, then rendering and reading the HTML — so a normal tutorial should (almost) never ask a student to *do* something in the R Terminal. The standing exceptions are `show_file()`, used to submit the contents of a file or chunk as evidence, and the occasional R-package install. We do **not** add an explicit step to load `library(tutorial.helpers)` — students learned to do that in `vscode.tutorials` — but we always include the standing reminder that *if `show_file()` fails, it is probably because `library(tutorial.helpers)` has not been loaded in the R Terminal.* There are no other "load `library(...)` in the R Terminal" exercises — every library the analysis needs goes in the QMD's setup chunk, and every result the student inspects comes from the render.

### Caching — every tutorial, once

- `#| cache: true` is a **render-time** feature — the cache is created during `quarto render`, not by running code interactively. The first render with caching takes noticeably longer; subsequent renders load from disk.
- **Every tutorial walks students through caching at least once.** Caching is a core concept worth reinforcing in every tutorial, exactly as the `.gitignore` step is — not a mere optimization to reach for only when a tutorial happens to be slow. Place the caching exercise on an expensive chunk, generally somewhere in the middle of the tutorial (it can go elsewhere). Tutorials with several expensive visualizations or data-preparation chunks may cache more than once.
- **Caching follows a standard three-exercise arc — never folded into another step.** Adding `#| cache: true` is a *refactoring* discipline, never combined with building, using, or copying the artifact. The setup is that the student has just built an artifact (function, fitted model, expensive tibble) and used it — either in a single chunk that defines and uses, or in two chunks where the to-be-cached one is followed by a working chunk that exercises it. The three-exercise arc that follows is:
  1. **Clean up.** Strip everything except the artifact's definition from the to-be-cached chunk. If the use of the artifact sits at the bottom of the same chunk, delete the use lines. If the use sits in a separate working chunk *after* the to-be-cached chunk, delete that whole working chunk. Either way, the last chunk of the QMD is now the cleaned-up to-be-cached chunk, holding only the definition and ready for `#| cache: true`. The question is `show_file("analysis.qmd", chunk = "Last")` → CP/CR, verifying the chunk is clean.
  2. **Cache and render.** Add `#| cache: true` to the chunk. Render the QMD. Rendering creates an `analysis_cache/` directory next to the QMD — the visible evidence that caching is wired up. The question is `ls` in the **bash Terminal** → CP/CR, verifying `analysis_cache` is now present in the file listing.
  3. **Edit `.gitignore`.** Mention (briefly) that the editor's Source Control change count has jumped because the new cache directory just appeared. Adding `analysis_cache` to `.gitignore` makes it drop back. The question is `show_file(".gitignore")` → CP/CR, verifying the edit.
- A tutorial's *first* cache uses the full three-exercise arc. If the tutorial caches a *second* artifact later, the `.gitignore` step is already done, so the second cache is just steps 1 and 2.
- After a cache arc, the student's working chunk is gone (cleanup deleted its contents). The next analytical exercise needs to **start a new working chunk**, not "modify the working chunk." Word the prompt accordingly.

---

## 3. The shape of one exercise

### The rhythm

Most exercises should follow this rhythm:

1. **Edit `analysis.qmd`** — change the topic's working chunk to do something concrete (in a topic's first exercise, create that chunk). See *One evolving working chunk per topic* (§2).
2. **Render** — students run `quarto render analysis.qmd` in a bash Terminal and inspect the rendered HTML.
3. **Verify** — students submit evidence that they completed the exercise. Use CP/CR only for terminal command-and-response evidence.
4. **Show the expected output** — after students submit and press Continue, show our expected output, answer, plot, tibble, or representative paste. **The author (Claude) is responsible for writing the text inside the backticks that shows students what they should see at the bottom of their rendered HTML.** Because an exercise almost always evolves the topic's *working* chunk (the last chunk in the document), this expected-output block must match that chunk's current result. Keep it accurate: when the data or analysis upstream changes, the displayed expected output has to change with it.
5. **Knowledge drop** — insert another `###` separator so students press Continue again before seeing the knowledge drop. Then provide a short paragraph that tells students what to notice, teaches domain knowledge, or foreshadows the next exercise.

The canonical loop is:

`prompt AI/edit analysis.qmd -> render -> inspect rendered HTML -> submit evidence -> expected output/answer -> Continue -> knowledge drop -> next exercise`

### The canonical question shape

Nearly every regular question looks exactly like this:

````
<prompt — one concrete goal; the student steers AI to do it. The student renders
and inspects the HTML as a matter of course, so the prompt need not spell that
out every single time.>

In the R Terminal, run `show_file("analysis.qmd", chunk = "Last")`. CP/CR.   <!-- always this for a QMD-edit question — whether the chunk produces a plot, a tibble, a summary, or a single value -->

```{r section-name-N}
question_text(NULL, answer(NULL, correct = TRUE), allow_retry = TRUE,
  try_again_button = "Edit Answer", incorrect = NULL, rows = 5)
```

###

```{r section-name-N-test}
#| echo: true
# our code — concise and modern (|>, never %>%)
```

###

<knowledge drop>
````

The **two `###` separators are load-bearing**. The first reveals our answer the instant the student clicks Continue — the `echo = TRUE` chunk shows our code *and* its result together. **Give that answer no label** — no "Our plot:", "Our code:", "Our result:" heading — because after Continue it is obviously ours; a label is noise. The second `###` makes the student pause on our code and result, comparing it to their own, before a final Continue reveals the knowledge drop. That knowledge drop does one or both of two jobs: it teaches a concept (often new terminology, like *faceting*), and/or it points out something specific in the result the student probably missed — ideally something that leads into the next exercise.

Showing our code *and* its result, via `echo = TRUE`, is the **default for every answer** — it is the whole point of "show our code afterward to compare against" (§2). A plain three-backtick block (code only) is an acceptable fallback, and the right choice when the code would take more than ~5 seconds to run, but the live `echo = TRUE` chunk is preferred.

**Which evidence to collect — always `show_file()` for QMD work.** A QMD-edit question always submits the *code*, via `In the R Terminal, run `show_file("analysis.qmd", chunk = "Last")`. CP/CR.`, **regardless of what the chunk produces** — a plot, a tibble, a `summary()`, a lazy-table preview, a single collected value, all the same. The student renders and reads the result in their HTML (that is their feedback loop), but what they *submit* is the chunk, so their code and our `echo = TRUE` answer sit side by side. There is **no separate "copy the printed result from the HTML" form**: a printed result is handled exactly like a plot — the student submits the code that produced it, and we show our code and run it to display the result. (Full rule in *Submission evidence*.)

**The two question kinds.** Setting aside the yes-answer conceptual question — a checked-answer *type*, not an evidence form (see *Question types*) — every working question is one of two:

1. **Edit the QMD → render → `show_file("analysis.qmd", chunk = "Last")`, CP/CR** — the default (the canonical shape above), for *everything done in the QMD*: plots, printed tibbles, summaries, a single collected number — all identical. The student submits the chunk's code; our `echo = TRUE` answer shows our code and its result. The whole-file check (`show_file("analysis.qmd")`, or `show_file("index.qmd")` for a website) (final state) and `show_file(".gitignore")` (file contents) are variants of this kind.
2. **Run a bash Terminal command → CP/CR** — when the exercise is operational or mostly changes the **set of files**: `ls` after creating `data/` or after a cache directory appears, plus `quarto render`, `quarto publish gh-pages`, `git add/commit/push`, `pwd`. The student runs the one command in the bash Terminal and pastes the command and its response; follow with the faked expected output (see *Submission evidence*).

Both submit via CP/CR — kind 1 from the R Terminal, kind 2 from the bash Terminal. "Copy the printed result from the HTML" is **not** a submission form.

The one further exception is an **interpretation/explanation question**: the student writes two or three sentences of prose in the QMD and submits it by **pasting that prose directly** — *"Paste your interpretation below."* — not via `show_file()`, because there is no code behind prose and `show_file("analysis.qmd", chunk = "Last")` would return the last *code* chunk instead. This is a small free-text category, distinct from the two evidence kinds above; our "answer" for it is a short model interpretation in prose (no code), or simply the knowledge drop. See §4, *Analysis path*.

### Question types

**No-answer questions (default).** Used for evidence-submission questions. Set `rows` to match expected output length.

```r
question_text(NULL,
  answer(NULL, correct = TRUE),
  allow_retry = TRUE,
  try_again_button = "Edit Answer",
  incorrect = NULL,
  rows = 5)
```

**Yes-answer questions.** Used when providing the correct answer (definitional or conceptual recall). Set `allow_retry = FALSE`.

```r
question_text(NULL,
  message = "Correct answer text here.",
  answer(NULL, correct = TRUE),
  allow_retry = FALSE,
  incorrect = NULL,
  rows = 6)
```

---

## 4. Structure of a whole tutorial

Every tutorial follows this order:

1. **Introduction** — overview of packages/functions covered; exercises to set up the repo, QMD, and libraries.
2. **Topic sections** — the *topics* are project-defined (see §7, *Choosing topics*, and your project's guide): `misc.tutorials` picks a data source / storage technology per tutorial; the Primer uses a fixed set — the Cardinal Virtues — the same in every tutorial. Either way, each section starts from data, follows an exploratory path, and usually ends with a useful plot or table plus a short interpretation.
3. **Summary** — mirrors the Introduction in past tense; if the tutorial publishes a result to the web, it finishes with the publish step and the resulting URL (and, for GitHub-based tutorials, a final commit/push).

**Don't quiz concepts the student hasn't reached.** In a sequenced tutorial set — especially one paired with a book — a tutorial's questions may only ask about concepts introduced in its own chapter or an earlier one. Never build an exercise on a concept the curriculum introduces later. (Light forward-pointers in knowledge-drop prose — "you will see this again when we reach X" — are a separate judgment call; keep them rare, per *no road signs*.) Which concept is introduced where is project-specific; consult the project's guide for the schedule.

### Introduction exercises (standard sequence)

1. Open with the **standard repo line**, verbatim: *"You should be connected to a repo named `whatever`. If you are not, create one and connect to it."* — where `whatever` is the tutorial's id (`r4ds-3`, `census`, …). Then create `analysis.qmd` with a **Title-Case** document title (see *Formatting conventions*), render it (this first render is the one place you spell out `quarto render analysis.qmd`), open `analysis.html` with Live Server, and **establish the "Render" shorthand** here — *"Open `analysis.html` with Live Server and keep the tab open. It refreshes on every render. Going forward, we will just tell you to 'Render' when we want you to take these steps."* Then create `.gitignore` with `analysis_files`, commit and push, and submit `show_file(".gitignore")` — followed by the standing note *"If that fails, it is probably because you have not yet loaded `library(tutorial.helpers)` in the R Terminal."* Our answer is the file's one line, `analysis_files`. Keep this lean — students past the infrastructure tutorials know how to make a repo, so don't re-walk that. **Do not name a specific environment.** These tutorials can be done locally just as well as in the cloud, so the intro must not mention Codespaces or the `codespace-starter` template; "create one and connect to it" deliberately covers both cases.
2. Add the libraries the tutorial needs in one chunk with `#| message: false`, add `execute: echo: false` to the YAML, render, and submit `show_file("analysis.qmd", chunk = "Last")`. Show our answer — the chunk — in a `<pre><code>` block (it contains `#|`, so four backticks would mangle it).
3. **Only if the tutorial downloads data to a local `data/` directory:** ask the student to **create a `data/` directory at the top level of the repo** — *state the goal, not the command* (they will usually have AI do it; that's fine). Confirm it with `ls` in the **bash Terminal**; CP/CR. Our answer is the faked `ls` output showing `data` now present. (Use the bash Terminal, **not** R-Terminal `dir.create()` — students should rarely issue R-Terminal commands directly, and bash fluency is itself worth building.) The canonical wording: *"Create a data directory at the top level of the `whatever` repo. In the bash Terminal, run `ls`."* then `CP/CR.` A live-API tutorial like `census` skips this step; its intro is just steps 1–2.

**No render-confirm exercise.** Do not add a standalone "render again and confirm the page refreshes" step — students already render and inspect Live Server in steps 1 and 2, so a third is pure boilerplate.

Replace `XX` placeholders with actual repo names, titles, and knowledge drops.

Keep introductions short. Avoid repeating details that the exercises will teach. If students must read several paragraphs before the first exercise, split the prose with Continue buttons; better yet, cut the prose down.

**Companion-text link.** If a tutorial is the companion to a specific chapter of a book or other text, its **first sentence** should name and link that **exact chapter** — not merely the work as a whole. A reader should be able to click straight through to the matching chapter. (Primer tutorials, for example, open by naming and linking the specific Primer chapter they support, in addition to the book.)

### Topic exercises

- Begin by getting data into the student's project. Often this means asking AI to create `data/`, download a file from a stable URL, and record where it came from.
- Ask for one concrete edit per exercise. Do not micromanage individual R function arguments unless the detail is pedagogically important.
- Render after every meaningful edit. The rendered HTML is the student's feedback loop.
- Somewhere in the topic sections, include the **caching exercise** (see §2, *Caching*): turn on `#| cache: true` for an expensive chunk and, in the same or the next exercise, add the cache directory to `.gitignore`. Every tutorial does this at least once.
- Use several linked exercises to build an analysis path: inspect the data, notice a pattern or problem, refine the data, make a rough plot, improve the plot, add interpretation.
- The final section should make the rendered artifact look good enough to publish (whether or not the tutorial actually publishes it).
- Before including a visualization exercise, ask what it shows that the preceding table or exercise did not. If the answer is nothing new — the same finding in a different form — cut the exercise and communicate the finding in the table's knowledge drop instead.

### Git commit exercises

Each topic section should end with a dedicated commit exercise placed as the last exercise in that section, after the final plot or analysis output. Do not fold it into another exercise. This per-section cadence is the **default**; a project may override it on the record (the Primer, for instance, commits only after Justice and at the end).

The exercise asks students to commit `analysis.qmd` with a specific descriptive message. Students may use any of three methods — all are acceptable:

- **AI agent**: "Ask AI to stage `analysis.qmd`, commit with the message `"Add X analysis"`, and push to GitHub."
- **VS Code Source Control panel**: click the branch icon in the sidebar, stage the file, enter the message, and sync.
- **Command line**: `git add analysis.qmd && git commit -m "Add X analysis" && git push` in a bash Terminal.

For GitHub-based tutorials (almost all of them), the Summary commit exercise (sequence step 3) should say "commit any remaining changes" — by that point the main content is already committed section by section.

Exception: if the tutorial has only one or two topic sections and the Summary's commit would cover all outstanding work, the final topic-section commit may be omitted to avoid asking students to commit twice in quick succession.

### Analysis path

Build topic sections from linked exercise units. A typical path:

1. Download or load data and document the source.
2. Inspect rows and individual variables before plotting relationships: printed tibbles, `summary()`, `count()`, histograms, missingness checks, top categories, random/sample rows, or `glimpse()` (avoid `glimpse()` on wide datasets — it prints one line per column).
3. Make students discover the data structure. If a variable will matter later, have students ask AI or consult documentation to learn what it means before using it in a final plot.
4. Notice a problem or opportunity: too many categories, missing values, outliers, awkward variable names, an important subgroup, unclear variable meaning, suspicious data structure, or an unclear plot.
5. Treat incomplete, sampled, truncated, or otherwise suspicious data as a teachable discovery. Ask students to uncover the limitation with summaries or plots, then explain what conclusions are and are not supported.
6. Ask AI to refine the analysis.
7. Make a rough plot or table.
8. Improve labels, grouping, ordering, scale, caption, and visual polish.
9. End each significant visualization with a dedicated interpretation exercise asking students to write one or two sentences about what the plot shows. This is distinct from the knowledge drop: the KD teaches; the interpretation exercise asks students to synthesize what they observed. Every major plot or table should have one.

**Structural exploration for downloaded or unfamiliar datasets.** Before moving to plots, add exercises that let students discover the dataset's shape and vocabulary. Three steps work well together, all done in the QMD's working chunk and read from the render: print the column names so students have them exactly when prompting; show the structure (types and a few example values) of a column group of ≤ ~15 columns; and show summary statistics for key numeric columns to catch placeholder values and establish scale before plotting. Do not use `glimpse()` on the full dataset when it has more than ~15 columns — split by column group first (e.g., `select(starts_with("artist")) |> glimpse()`). These exercises are most valuable for downloaded CSVs and unfamiliar datasets; they are less necessary for well-known built-in datasets where the column structure is self-evident from the first print. Place any "what does this variable mean?" exercise immediately after students first see the column name — not after they have already used it in a summary or plot.

**Categorical comparisons.** For comparisons across categories, scaffold the student's choice rather than prescribing it: first have students count observations per category so they can see what is plentiful, then ask them to choose two categories with enough data to compare (e.g., at least 100 rows each), then build the comparison plot. This gives students practice making an informed analytical decision rather than following a script.

**The most teachable datasets hide a discoverable anomaly.** The strongest analysis paths are the ones where a naive first look seems fine, but a clue hints that something is off, and a further step — very often a plot — reveals the mystery and then explains it. (The canonical example: a time-series plot of weekly chart rankings exposes a sharp discontinuity that the summary statistics completely hide.) When a dataset has this property, build the path so students *discover* the problem themselves rather than being told about it. Such structure is far more common in rich, sizable datasets than in small clean ones — a reason to favor richer data when the choice is open (topic/data selection itself is project-specific; see §7, *Choosing topics*, and the project guide).

The final artifact should be a finished product with a meaningful result about the world, not just a completed worksheet — a published page or website for the common case, or simply the rendered local document when the tutorial puts nothing on the web.

For histograms and other distribution plots, usually include the number of observations used in the plot, especially when missing values or filters may exclude rows. Put `n` in the subtitle, caption, or nearby knowledge drop so readers know whether they are seeing the full dataset or only available values.

### Summary exercises (standard sequence)

1. **Final local test of the finished product**, then submit evidence — but the evidence matches what the tutorial builds. A single rendered page: final `quarto render`, then `show_file("analysis.qmd")` for the whole QMD, so we keep a record of what it looked like. A **website** (a Quarto project with `_quarto.yml`): render the site, then `show_file("index.qmd")` (whatever the landing file is). The point is a last look at the real finished artifact before it goes out.
2. **Publish the final version to the web — only if the tutorial puts a result there.** Match the command to what the tutorial builds: a single page uses `quarto publish gh-pages analysis.qmd`; a website publishes the whole project (e.g. `quarto publish gh-pages` from the project root, or the host-specific command). The accompanying question is always about the **URL of the published product**. **Omit this step entirely** for tutorials that produce nothing on the web.
3. **Commit and push — only for GitHub-based tutorials.** By this point everything is usually committed already, so this just sweeps up any remaining changes; the question asks for the **GitHub repo URL**. **Omit** for tutorials that don't use GitHub.

Most tutorials build a single page, publish it to Quarto Pages on GitHub, and use a GitHub repo — so most include all three steps in that form. But none of the three is automatic: choose step 1's evidence by what the artifact is, and treat 2 and 3 as conditional on whether the tutorial publishes to the web and whether it lives on GitHub.

---

## 5. Writing rules

### Writing student prompts to AI

Describe the **goal**, not the implementation. Students should tell AI what they want to see — a plot of X by Y, a table of the top 10, the distribution of Z — and let the AI choose the functions. This is how professionals actually work, and it gives students practice evaluating whether the AI's approach is correct.

**Don't narrate the AI.** State each task as a direct instruction — *"Add a chunk to `analysis.qmd` that plots X by Y, then render"* — not *"ask your AI agent to add a chunk that…"*. Say **once**, in the first exercise, that the student may use AI however they like (e.g. *"Use AI however you like."*); after that, every exercise simply states the goal, and the student knows AI is how they get there. Repeating "ask your AI agent to…" on every exercise is noise.

- **Do not** list functions for students to include in their AI prompt (e.g., "use `geom_histogram()` and `scale_x_log10()`").
- **Do not** dictate the pipe steps or function arguments students should pass to AI.
- **Never paste a block of analysis code for the student to copy.** State the goal — what the code should *produce* — and let AI write it. If a goal is too complex to state cleanly, split it into several smaller questions that build the result step by step ("first read the file and pull out the features; then flatten those into a table; …"). It is fine if the student can't produce it on their own — that is exactly why the question is followed by our good code and its result. (Giving the `library()` lines to load is the one routine exception: that's setup, not analysis.)
- **When a transformation is itself the lesson** (reshaping rows↔columns, joining tables, aggregating groups), state it as a **concept and a goal**, not a function — "reshape the data so each variable is its own column, which makes the plot easier," not "use `pivot_longer()`." The student should carry away what reshaping *is* and *why* it helps later analysis and plotting; the function name is noise. There is essentially no case where a function name in a student's prompt beats describing what they want and why.

### Knowledge drops

**Every knowledge drop in a normal tutorial does one of three jobs.** The generic, reusable drops the infrastructure tutorials lean on — *"Professionals keep their data science work in the cloud because laptops fail,"* *"the tidyverse is a family of packages…"*, *"QMD World and R World are not the same"* — belong **only** in `vscode.tutorials`. In a normal tutorial, a knowledge drop must instead:

1. **Make a key point from the companion book chapter** (when there is one). Students usually don't read the chapter, so the tutorial is where we *pull out* the points they most need to carry away.
2. **Talk about the data** the tutorial works with — its source, its units of observation, how it was collected, a quirk or limitation worth knowing.
3. **Comment on what the most recent command displayed** — point out something in the plot or table the student just produced that they probably didn't notice, or that guides them toward the next question.

If a drop isn't doing one of these three, cut it. Do **not** reach for a canned infrastructure lesson to fill the slot.

**The third kind is the most common.** Most knowledge drops are simply an observation about the result the student has just displayed — something interesting in the plot or table that she may not have noticed. Very often that observation leads directly into the next question, so the drop doubles as the setup for what comes next.

- Every exercise must have a knowledge drop after submission, and it must be one of the three kinds above. This holds even when the exercise is mostly setup, committing, publishing, or verification — for those, pull a key point from the chapter or say something about the data, rather than narrating the mechanics.
- Usually one short paragraph; one or two sentences is often enough.
- Keep drops **brief in word count, but they may be richer visually**: a small concrete example — a five-line sample of the raw file format, a tiny named list, a one-line snippet — often teaches a concept far better than prose. Show the example and stop; do not follow it with discussion.
- Teach what something **is**, with a simple example, rather than narrating what a package *can do* with it. The reader assumes the package works ("would be surprised if it couldn't"); the concept is what they need to carry away. Prefer *"a named list labels each element — `list(name = ..., year = ...)`"* over *"`tidyr` can unnest a named list-column."*
- Place after students submit an exercise and after the expected output/answer. The usual pattern is question chunk, `###`, expected output/answer, `###`, knowledge drop.
- Place before the question only when context is needed to answer.
- Reference our example output when the test chunk displays a plot, tibble, or summary. When the test chunk reveals specific numbers, use them — "496 zero values (nearly 5%)" rather than "many zero values." Concrete numbers make knowledge drops credible and easier for students to verify against their own output.
- Tell students what to notice in their rendered output, explain why it matters, or identify the next natural data-science move.
- In the age of AI, knowledge drops should emphasize concepts students need to steer and audit AI-generated work: data shape, units of observation, missingness, filtering, variable meaning, plot interpretation, domain context, and whether a result supports the claim being made.
- When AI handles syntax or transformations under the hood, name the concept and explain it in plain language after students see the result. Students do not need to memorize syntax immediately, but they do need to recognize what AI did and why it matters.
- Do not use recycled/default knowledge drops in post-infrastructure tutorials. Save repeated infrastructure lessons for the `vscode.tutorials` infrastructure tutorials; later tutorials need knowledge drops tied to the current exercise, current data, or current infrastructure issue.
- Use knowledge drops to teach the data science ecosystem for the tutorial's area: gold-standard data sources, common measures, important packages, file formats, APIs, data-quality issues, and standard patterns analysts look for.
- **Discuss packages and libraries, not specific functions.** Students don't write code, so they don't need to learn individual functions — a knowledge drop should rarely name one. What students *do* need is a map of the **infrastructure**: which packages exist, what each is for, and which alternative packages they could have used for this job even though the tutorial didn't (e.g. mention **data.table** or **arrow** alongside **dplyr**; **httr2** alongside a domain API package). We teach infrastructure, not code, and packages are part of the infrastructure. When a transformation matters, teach the **concept** and the reason for it — "turning rows into columns so each variable has its own column makes plotting and modeling easier" — not the function that does it (`pivot_wider()`). The concept transfers; the function name does not.
- No road signs ("In the next section..."). Teach something real.
- No rhetorical questions.

### Submission evidence

CP/CR means **Copy/Paste the Command/Response**. Use CP/CR only for terminal or R Terminal submissions where students paste both the command they ran and the response they got. Do not add extra wording like "the command and response" or "the terminal output"; that is already implied by CP/CR. Assume students already know what CP/CR means — they learned it in the `vscode.tutorials` infrastructure tutorials — so do **not** explain it in a normal tutorial; just use the shorthand. Put the `CP/CR.` instruction on its own line at the end of the prompt.

**The evidence forms — just two.** A QMD-edit question submits code via `show_file()` (R Terminal); an operational/file-set step is a bash command (bash Terminal). Both are CP/CR. **There is no copy-from-HTML form.** Pick by whether the exercise edits the QMD or touches the shell:

- **Anything done in the QMD → `show_file("analysis.qmd", chunk = "Last")`** (CP/CR) — the default, and by far the most common. The student edits the working chunk, renders to see the result in the HTML, then submits the chunk's *code*; we answer with the `echo = TRUE` chunk that shows our code and runs it to display the result. This holds **whatever the chunk produces** — a plot, a printed tibble, a `summary()`, a `dbListTables()`, a lazy-table preview, a single collected value. A printed result is submitted exactly like a plot; do **not** ask the student to copy output from the HTML, and do **not** treat "printed data" as a separate form. (R Terminal.)
- **File contents / final state → `show_file()`** (CP/CR) — `show_file(".gitignore")`, the whole-file final check (`show_file("analysis.qmd")`, or `show_file("index.qmd")` for a website). A variant of the above. (R Terminal.)
- **File-set change or operational step → bash Terminal command → CP/CR.** When the exercise mostly changes the set of files (or is best examined from the shell): `ls` after creating `data/` or after a cache directory appears, plus `quarto render`, `quarto publish gh-pages`, `git add/commit/push`, `pwd`. The student runs the one command in the bash Terminal; the command and its response are the evidence.

**Showing our answer.** Almost every exercise should be followed — as the *first* thing after the student submits and clicks Continue — by **our answer**. Present it as a single chunk with `echo = TRUE` (the §3 pattern), which both shows our code and runs it to display the result, so the student can compare our code *and* our output against their own. Do not reproduce the student's submission, and — for any `show_file()` exercise (plot, printed result, or file) — do not show the `show_file()` call itself; just show the analysis code we expect, computed on the fly. Give it **no label** — no "Our plot:" or "Our code:" heading; after Continue it is obviously ours. A plain three-backtick block (code only) is an acceptable fallback, and the right choice when the code would take more than ~5 seconds to run, but the live `echo = TRUE` chunk is preferred.

**Fake the output of terminal commands you can't run in a chunk.** We almost *always* follow a question with our best guess at what the student will — or at least should — see. For R code that is the `echo = TRUE` answer chunk. For a bash/R-Terminal command whose result you cannot reproduce in a live chunk (`ls data`, `pwd`, render messages), you still show the answer: a plain code block with your best guess at what the command prints — e.g., the file(s) `ls data` would list. Do not leave it as prose ("Your `data` directory should now include `X`"); show the faked output instead. Follow our answer with a `###` so the student presses Continue again before the knowledge drop. Keep our code concise and modern — always `|>`, never `%>%` — because the whole point is that the student studies it and compares it with their own.

**The two `show_file()` usages — and how the faked answer differs.** `show_file()` is called one of two ways, and our faked answer differs by which:

- **`show_file("analysis.qmd", chunk = "Last")`** — returns only the *inner lines* of the last code chunk (its `#|` options and code), with no ` ```{r} ` fence and no `> show_file(...)` prompt. Our answer is exactly those inner lines, nothing else. Because the text carries no chunk fence, nothing trips knitr — this is the default working-chunk evidence and already works across the tutorials.
- **`show_file("analysis.qmd")`** (no `chunk` argument) — returns the *entire file*: YAML, prose, and every code chunk **with** its ` ```{r} … ``` ` fences. This is the whole-QMD check in the Summary (`analysis.qmd`, or `index.qmd` for a website); `show_file(".gitignore")` and other single-file checks are the same no-argument form on a different file. Our faked answer must reproduce the file **faithfully — fences included** — so it matches what the student actually sees when they run the command. **The catch:** knitr scans the document line by line and *executes* a ` ```{r} ` fence on its own line **even inside `<pre><code>`** (verified — the wrapper does *not* protect an own-line fence; a fence left as literal backticks runs the code, and a tutorial that pulls live data hits the network and the render dies). **The fix is not to delete the fences but to escape their backticks: write each fence as `&#96;&#96;&#96;{r}` and `&#96;&#96;&#96;`.** The browser renders `&#96;` as a literal backtick, so the reader sees the exact ` ```{r} ` / ` ``` ` fences `show_file` printed, while knitr — which matches literal backticks, not the HTML entity — sees no chunk and runs nothing. Wrap the whole reproduction in `<pre><code>` (which also lets you escape `&lt;your name&gt;`). (A file with no chunks — `.gitignore`, plain text — has no fences and needs no escaping.)

**Two non-negotiables for the faked answer, especially for `show_file()` results:**

1. **Never leave a *literal* own-line ` ```{r} ` or ` ``` ` fence in the answer — knitr executes it even inside `<pre><code>`.** For a **whole-file** answer, reproduce every fence but escape its backticks as `&#96;&#96;&#96;`, so the reader sees the fence while knitr does not detect a chunk. For a **`chunk = "Last"`** answer there is no fence to reproduce at all (`show_file` returns the inner lines only), so the question never arises. Either way, wrap the result in `<pre><code>…</code></pre>` and HTML-escape any tag-like `<` (`&lt;your name&gt;`, `&lt;your-github-username&gt;`) so the browser doesn't parse it as a tag.
2. **Strip the `> show_file(...)` command line.** `show_file()`'s literal output is preceded by the `> show_file(...)` prompt the student typed at the R Terminal. Our canonical answer never echoes that prompt — it begins with the file's first line (the YAML `---` for a whole file, or the first `#|`/code line for a `chunk = "Last"` answer). The student's paste includes the prompt; ours doesn't.

So for a question whose evidence is `show_file("analysis.qmd", chunk = "Last")` on the chunk containing `#| message: false` and `library(tidyverse)`, our answer is:

```
<pre><code>#| message: false
library(tidyverse)
</code></pre>
```

— not four backticks wrapping ` ```{r} … ``` `, and not the `> show_file(…)` prompt above the code.

**The "if your result doesn't match ours, replace your code" fallback is now rare — and never a second paste.** We ask a question, then show our code and its result (the `echo = TRUE` answer). Most of the time that is the end of it: the next exercise rewrites the chunk anyway, and small differences from our code don't matter. Only when it genuinely matters — we are about to **save an object whose shape the rest of the section depends on** (a cached data pipeline) — do we add a short line asking the student to compare, and, if their result differs, replace their code with **the code we just showed above**. Never re-paste the code in a second block, and never make the student paste two pieces; if anything is pasted at all, it is one chunk. And keep **caching a separate question** — never fold "add `#| cache: true`" into the copy-our-code question.

**Two terminals, two roles.** The **R Terminal** is rarely used — except to run `show_file()` to answer a tutorial question (the default evidence form, so it comes up constantly) or to install R packages. The **bash Terminal** carries the file-set and operational commands (question kind 2) — `quarto render`, `quarto publish`, `git …`, `ls`/`pwd` — collected CP/CR; prefer it for anything shell-shaped (e.g. create a `data/` directory and confirm with `ls`, not R-Terminal `dir.create()`), and building bash-Terminal fluency is itself a goal. Per §2 (*Almost everything happens in the QMD*), **never run *analysis* in the R Terminal** — anything that produces analysis output belongs in a QMD chunk, rendered, and read from the HTML.

### Formatting conventions

- Keyboard input and inline code: `backticks`
- Package names in prose: bolded throughout, and the **first mention in a tutorial** is also linked to the package's gold-standard documentation site — usually the official package website (e.g. **[ggplot2](https://ggplot2.tidyverse.org/)**, **[dplyr](https://dplyr.tidyverse.org/)**). Later mentions stay bold with no repeated link. Do not link package names inside code.
- Function names always include parentheses: `read_csv()`, not `read_csv`
- Section/topic titles: sentence case
- Document titles (the `analysis.qmd` title) use **Title Case** — e.g. `"Sampling"`, not `"sampling"`. (Distinct from section/topic headings, which stay sentence case.)
- Terminal names are **capitalized, including the shell name**: `R Terminal` and `bash Terminal` — always a capital `T`, never "bash terminal".
- VS Code's file sidebar is the **Explorer** — not "File Explorer", which is the Windows OS file manager.
- Write **"Commit and push"**, never "Save and push" — saving is assumed (the AI does it), and you cannot push without committing first.
- Abbreviation: CP/CR = Copy/Paste the Command/Response

---

## 6. Mechanics and checking

### Code chunk conventions

- Label format: `section-name-N` and `section-name-N-test` (e.g., `billboard-3`, `billboard-3-test`).
- Run `tutorial.helpers::check_current_tutorial()` after adding/deleting exercises to renumber everything.
- Use `tutorial.helpers::make_exercise()` to add new exercises with correct numbering.
- `echo = FALSE` everywhere (set globally in setup chunk via `knitr::opts_chunk$set(echo = FALSE)`). The routine exception is the `echo = TRUE` answer chunk shown after the question (see §3 and *Submission evidence*).
- **Per-chunk options use Quarto's `#| key: value` syntax on lines inside the chunk, not inline `, key = value` on the header.** So the answer chunk is

  ```
  ```{r section-name-N-test}
  #| echo: true
  # our code
  ```
  ```

  not `{r section-name-N-test, echo = TRUE}`. This works in both `.Rmd` and `.qmd` via modern knitr (≥ 1.35) and is the canonical style across the curriculum — use it for `echo`, `message`, `warning`, `cache`, `eval`, and every other chunk option. The only inline options that remain on the header line are `include = FALSE` on the setup chunk and the `child = ...` argument on info-section / download-answers child chunks.
- Use the native pipe `|>` in all displayed code, never the magrittr `%>%`.
- **Prefer a single pipe expression over intermediate variables.** In test-chunk answer code, chain operations from source to result in one unbroken pipe rather than assigning partial results to named intermediate variables. Intermediate assignments obscure the data flow, add names that mean nothing outside their three-line scope, and make it harder for students to study the whole transformation at once. Break the chain only when a genuinely reusable object is created — one that appears in more than one downstream expression — or when a step is expensive enough to cache on its own.
- Set `knitr::opts_chunk$set(out.width = '90%')` in setup for consistent image sizing.
- Avoid exercise code chunks in post-infrastructure tutorials. Use question chunks for evidence submissions and test chunks for our example output.
- Students evolve one working chunk per topic rather than adding a chunk per exercise; a finished `analysis.qmd` is the setup chunk plus roughly one chunk per topic (plus any separate data-download/preparation chunks). See *One evolving working chunk per topic* (§2).
- Do **not** include the `copy-code-chunk` (copy-button) child document. Its only purpose is to add a copy button to exercise code chunks, and these tutorials have none. The `info-section` and `download-answers` child documents stay.

### Setup chunk requirements

```r
library(learnr)
library(tutorial.helpers)
library(tidyverse)
# ...other libraries...

knitr::opts_chunk$set(echo = FALSE)
knitr::opts_chunk$set(out.width = '90%')
options(tutorial.exercise.timelimit = 600, tutorial.storage = "local")
```

Pre-compute only what the tutorial itself needs to show examples after submission. Do not load data merely to support student exercise code chunks in post-infrastructure tutorials.

### Test chunk output

Test chunk output should be scannable in a few seconds. If it scrolls, it is too long. Apply these fixes before committing:

**Wide tibbles (many columns)** — `glimpse()` prints one line per column; on a 79-column dataset that is 81 lines. Print the tibble directly instead (`billboard`, not `glimpse(billboard)`): tibbles auto-truncate to 10 rows and summarise extra columns on one line. Reserve `glimpse()` for narrow datasets (≤ ~15 columns) where the column-by-column view is genuinely useful.

**CSV column-spec messages** — `read_csv()` prints a column-spec block (one line per column) before the tibble. Suppress it with `show_col_types = FALSE`:

```r
read_csv("data/music.csv", show_col_types = FALSE)
```

**Long model or computation output** — wrap with `suppressMessages()` or redirect progress with `refresh = 0, silent = 2` (for **brms**). Only show the final object students need to check.

**Plots** — always fine; they produce a single visual, not scrolling text.

**The rule of thumb**: if the rendered test chunk output is taller than a laptop screen, shorten it.

### Data handling

- Never depend on a fragile third-party URL at tutorial run time.
- **A tutorial's own source data lives in a `data/` directory inside the tutorial folder** — `inst/tutorials/<name>/data/<file>`, next to `tutorial.Rmd` and alongside `images/`. The package's setup and test chunks read it with the *same relative path a student writes* — `read_csv("data/music.csv")`, not `../../extdata/...` — because a tutorial knits with its own folder as the working directory. Putting the data where the student puts it makes our answer code identical to the student's code. Document each file's provenance in a short `data/README.txt` — and add that README to `.Rbuildignore` so the note stays in the repo but is not installed with the package (the data itself under `data/` still ships).
- **Student-facing download URLs point at that same in-tutorial location on GitHub** (`.../raw/refs/heads/main/inst/tutorials/<name>/data/<file>`). Students download into their own project's `data/` directory and read `data/<file>`.
- Students usually create their own `data/` directory inside their project and download or copy data there.
- **Match the download instruction to where the tutorial sits in the sequence.** In a student's *earliest* data-science tutorials, an explicit, working command — `download.file("<url>", "data/<file>")` — is a helpful scaffold; giving it is fine. In later tutorials, just tell them to download the file from the stable URL into their `data/` directory and let them choose whether to run `download.file()` themselves or ask AI to fetch it — they will usually ask AI, which is fine. Either way, confirm with `ls data` and show that the file is now present. (A tutorial where the download *mechanics* are themselves the lesson always spells out the command.)
- Shipping data inside `inst/tutorials/<name>/data/` means it is built into the package, which can push a data-heavy package over CRAN's size limit. That is an accepted trade-off for having the package's code match the student's; a project records how it handles distribution (CRAN or not) in its own guide.
- Do not download from the web during tutorial compile/run. If a test chunk needs data, load a small stable copy from the package.
- For large data, create smaller teaching files and use lightweight evidence submissions rather than heavy test computations.
- If a teaching dataset is incomplete, sampled, capped, or otherwise artificial, do not hide that fact. Build an exercise path that lets students discover the limitation and reason about how it affects interpretation.

### Images

- Store in `images/` directory alongside `tutorial.Rmd`.
- Include with `knitr::include_graphics("images/example.png")` in an unnamed chunk.
- Requires `library(knitr)` in the setup chunk.

### Displaying code verbatim in tutorials

Use `<pre><code>…</code></pre>` HTML wrappers (not four backticks) to display code blocks verbatim inside tutorial prose. Four-backtick fences look right in source but knitr eagerly parses the inner ` ```{r} ` as a real chunk and the literal source never reaches the rendered HTML; raw HTML `<pre><code>` bypasses knitr.

When the content represents a **single** QMD chunk (our answer for a `show_file("analysis.qmd", chunk = "Last")` question), include only the **code inside** the chunk — `#|` options and code lines — and **omit the ` ```{r} ` opening and ` ``` ` closing wrappers**. The wrapper is QMD source noise; what matters is the code, and stripping the fence also means there is nothing for knitr to misparse. (See *Showing our answer* and *The two `show_file()` usages* in §5 for the full rule.) Example — to display the canonical state of a setup chunk containing `library(tidyverse)`:

```
<pre><code>#| message: false
library(tidyverse)
</code></pre>
```

When the content is a **whole QMD file** (our answer for a `show_file("analysis.qmd")` question with no `chunk` argument), reproduce the file **faithfully — fences and all** — so the answer matches what `show_file` prints: the YAML, the prose, and every chunk with its ` ```{r} … ``` ` fences. The one adjustment is that you must **escape the fence backticks as `&#96;&#96;&#96;`**, never leave them literal: knitr scans line by line and *executes* a literal own-line fence **even inside `<pre><code>`** (the wrapper does not protect it), so a whole-file answer that keeps literal fences will run — and a tutorial that pulls live data will hit the network and the render dies. The browser renders `&#96;` as a backtick, so the reader still sees real ` ```{r} ` / ` ``` ` fences, while knitr sees no chunk. Example — a two-chunk `index.qmd`:

```
<pre><code>---
title: "Baseball"
---

&#96;&#96;&#96;{r}
#| message: false
library(tidyverse)
&#96;&#96;&#96;

## Home runs over time

&#96;&#96;&#96;{r}
#| echo: false
plot(hr_per_game)
&#96;&#96;&#96;
</code></pre>
```

(A file with no chunk fences — `.gitignore`, plain text — has nothing to escape.)

HTML-escape any tag-like `<` placeholders inside the block (`&lt;your name&gt;`, `&lt;your-github-username&gt;`) so the browser doesn't parse them as opening tags. `<-` and `|>` in R code are fine — they don't look like tag starts and browsers display them as text.

When showing multiple commands for students to copy/paste, make sure they render as separate lines. Do not let Markdown collapse separate commands into one paragraph.

### Authoring with AI agents

When changing or creating subject-area tutorials, use Claude/Gemini iteratively:

- Ask the agent to inspect the existing tutorial and propose changes before editing.
- Correct the agent's plan when it misses project rules, especially the render + Live Server workflow, no post-infrastructure exercise code chunks, data handling, and knowledge drops.
- Make one section correct first. Render it, inspect the result, and revise before applying the same pattern elsewhere.
- Ask the agent what it will do in the next section before it edits.
- Keep expected output, prose, prompts, knowledge drops, and displayed example plots in sync when the data or analysis changes.

### Checking a tutorial

**Render early and often.** Run `rmarkdown::render()` after essentially every change, not just at the end — it is the fastest way to catch a broken chunk, a stray warning, or output that no longer matches the prose. A normal tutorial should render in **not much more than 15 seconds**; if it takes much longer, treat that as a signal (an uncached expensive chunk, an accidental web call) worth fixing. Read the render's console output, not just the exit status: warnings like `NAs introduced by coercion` are defects to fix, not noise to ignore.

**Always confirm numbering after adding, deleting, or reordering exercises.** Exercise headers (`### Exercise N`) and chunk labels (`section-name-N`, `section-name-N-test`) must stay sequential and in sync within each section. Confirm it either by reading the file directly or with `tutorial.helpers::check_current_tutorial()`.

1. **Quick syntax check**: `rmarkdown::render("inst/tutorials/name/tutorial.Rmd")` — open resulting HTML in browser. Do this constantly while authoring.
2. **Full check**: `devtools::check()` (`Cmd/Ctrl + Shift + E`) — must pass before any PR.
3. **Student perspective**: `devtools::install()` then `learnr::run_tutorial("tutorial_name", "<your package>")` (e.g. `"misc.tutorials"` or `"primer.tutorials"`).

`devtools::check()` validates the default `tutorial_template` child documents. Keep `info-section` and `download-answers`; do not remove or alter them. The `copy-code-chunk` child is no longer used (see *Code chunk conventions*) — if `check()` still flags its absence, that check in `tutorial.helpers` is what needs updating, since the copy button has no role once there are no exercise code chunks.

### DESCRIPTION

Any library loaded in a tutorial must appear under `Imports` or `Suggests` in `DESCRIPTION`. `devtools::check()` on GitHub Actions will fail if a package is `library()`-ed but not listed.

---

## 7. Choosing topics

How a tutorial's subject is chosen is **project-specific** — the one genuinely package-dependent part of this guide. Each project's own guide defines its topic model; consult it before starting a tutorial. Two examples: `misc.tutorials` organizes tutorials around data sources / storage technologies and real data science domains (the per-tutorial teaching checklist lives in that package's guide); the Primer fixes its topic sections as the four Cardinal Virtues, identical in every tutorial. Whatever the model, each topic section starts from data, follows an exploratory path, and ends with a useful plot or table plus a short interpretation — that part is universal (see §4, *Tutorial structure*, and *Analysis path*).

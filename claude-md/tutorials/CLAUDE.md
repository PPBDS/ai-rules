# CLAUDE.md — Tutorial authoring guide

This is the authoring guide for **"normal" tutorials**: the data science tutorials students do *after* they have learned the basic mechanics of their tools. It is not specific to any one package. The same guidance applies to any **learnr** tutorial that teaches a data science topic to students who already know how to work in their environment — across `misc.tutorials`, the Primer, and any other tutorial package in this ecosystem.

**This guide is the default contract for every such tutorial.** Unless a tutorial is one of the explicit exceptions below, it follows everything here by default. A project may override a rule — but the override must be **explicit**: stated in that project's own guide, named as a departure from this base, and justified. Silent divergence is a bug. Project guides (e.g. the Primer's) read in and accept this file first, then add only what is specific to them or override it on the record.

These tutorials assume students already understand the foundational skills: Git, GitHub, `.gitignore`, `_files` directories, terminals, Codespaces, and Quarto rendering. Tutorials written under this guide do **not** re-teach those mechanics.

**The exception is two whole packages, not a list of tutorials.** This guide does **not** apply to any tutorial in the `vscode.tutorials` package or the `tutorial.helpers` package. Those packages teach the tools and mechanics themselves — Git, GitHub, Codespaces, Quarto, the `tutorial.helpers` exercise system — which is precisely what this guide assumes students have already done. Every *other* tutorial follows this guide by default: `misc.tutorials`, the Primer, and any future package. There is no need to enumerate which tutorials are in or out — membership in `vscode.tutorials` or `tutorial.helpers` is the entire exception.

## Scope and what is universal

This guide governs **tutorials** — learnr tutorials specifically. Other artifacts a project may produce (textbook chapters, classroom exercises, slide decks) are **out of scope** here; each such artifact defines its own relationship to this guide. The Primer, for example, applies this guide to its tutorials but not to its prose book chapters.

Almost everything below is **universal** — true of any normal tutorial regardless of package. The one section that is genuinely package-specific is **Choosing topics (misc.tutorials-specific)**, which describes how `misc.tutorials` selects each tutorial's subject; other projects replace it (the Primer fixes its topics as the four Cardinal Virtues, identical in every tutorial). A few rules state a **default value or cadence** a project may override on the record (per the contract above); those are noted inline.

## Project layout

Tutorials are **learnr** tutorials shipped inside an R package (e.g. `misc.tutorials`, `primer.tutorials`), living in `inst/tutorials/<name>/` as `tutorial.Rmd` files:

```
inst/tutorials/<name>/
  tutorial.Rmd       # main tutorial file
  images/            # images included via include_graphics()

inst/extdata/<name>/ # stable source copies of data students may download
```

## Philosophy (AI era)

The goal is not to teach coding — it is to teach students how to **use AI to create data science artifacts**. Once students are past the infrastructure tutorials, they should mostly interact with an AI agent that edits their files, renders their Quarto document, and helps them debug. They should learn to steer analysis, inspect output, notice problems, and ask for refinements.

- These are data science tutorials. They should follow an exploratory path through data toward a useful published artifact.
- Show students **results** (plots, printed tibbles, summary statistics, rendered pages), not just the code that produced them.
- Questions ask students to prompt AI for file edits, render, inspect output, and compare their output to ours.
- Prefer many small exercises that form a data analysis path over one large prompt that solves the whole section.
- Students should not type R into learnr exercise chunks in post-infrastructure tutorials. Their work happens in `analysis.qmd` and supporting files.
- `echo = FALSE` is the default everywhere. Only reveal code when there is a strong pedagogical reason.
- Test chunks (`exercise-N-test`) may produce visible output after students click Continue — this is how we give them something concrete to check against, such as our example plot or tibble.

## Student workflow

Students view all QMD output via **render + Live Server**, not by running code in the R Terminal:

- After editing `analysis.qmd`, students run `quarto render analysis.qmd` in a bash terminal.
- They open `analysis.html` with Live Server once at the start (right-click in File Explorer → "Open with Live Server"); it auto-refreshes on every subsequent render.
- **Never** instruct students to use `Cmd/Ctrl + Enter` to run QMD code or `Cmd/Ctrl + Shift + K` to render.
- `#| cache: true` is a **render-time** feature — the cache is created during `quarto render`, not by running code interactively. The first render with caching takes noticeably longer; subsequent renders load from disk.
- Cache chunks when students are done with an expensive piece of code. Most tutorials should use caching at least once; tutorials with several expensive visualizations or data-preparation chunks may use it more often.
- Add the generated cache directory (usually `analysis_cache`) to `.gitignore`; cache files do not belong on GitHub.

**Almost everything happens in the QMD, not the R Terminal.** Students do their work by prompting the AI agent to edit `analysis.qmd`, then rendering and reading the HTML — so a normal tutorial should (almost) never ask a student to *do* something in the R Terminal. The standing exception is `show_file()`, used to submit the contents of a file or chunk as evidence. `tutorial.helpers` is loaded for the student so `show_file()` works; loading it is **not** a student-facing step, and there are no "load `library(...)` in the R Terminal" exercises — every library the analysis needs goes in the QMD's setup chunk, and every result the student inspects comes from the render.

## Choosing topics (misc.tutorials-specific)

*This section is `misc.tutorials`' content model — how it picks each tutorial's subject. Other projects replace it; the Primer's topic sections are the four Cardinal Virtues, fixed across every tutorial, so the Primer ignores the subject-area guidance here.*

In `misc.tutorials`, tutorials should be organized around prominent data sources and real data science domains. Examples: US Census data, baseball data, stock data, Bitcoin, and other subject areas where students can learn what analysts actually use.

Each subject-area tutorial should teach:

- The gold-standard data sources for that area.
- The main R packages, APIs, file formats, and vocabulary students should mention to AI.
- Common data patterns, data quality issues, and standard analytical questions in that domain.
- A reproducible workflow that ends with a small published Quarto artifact.

## Tutorial structure

Every tutorial follows this order:

1. **Introduction** — overview of packages/functions covered; exercises to set up the repo, QMD, and libraries.
2. **Topic sections** — the *topics* are project-defined: in `misc.tutorials` each tutorial picks a subject-area domain (see *Choosing topics*); in the Primer the topic sections are a fixed set — the Cardinal Virtues — the same in every tutorial. Either way, each section starts from data, follows an exploratory path, and usually ends with a useful plot or table plus a short interpretation.
3. **Summary** — mirrors the Introduction in past tense; finishes with `quarto publish gh-pages` and a GitHub URL.

**Don't quiz concepts the student hasn't reached.** In a sequenced tutorial set — especially one paired with a book — a tutorial's questions may only ask about concepts introduced in its own chapter or an earlier one. Never build an exercise on a concept the curriculum introduces later. (Light forward-pointers in knowledge-drop prose — "you will see this again when we reach X" — are a separate judgment call; keep them rare, per *no road signs*.) Which concept is introduced where is project-specific; consult the project's guide for the schedule.

### Introduction exercises (standard sequence)

1. Confirm you're already in your repo (you created it to launch the Codespace), then create `analysis.qmd` with a **Title-Case** document title (see *Formatting conventions*), render, open `analysis.html` with Live Server, set up `.gitignore`, commit and push, submit evidence. Keep this lean — students past the infrastructure tutorials know how to make a repo and open a Codespace, so don't re-walk that or re-link the `codespace-starter` template.
2. Add `library(tidyverse)` to QMD with `#| message: false` and `execute: echo: false` in YAML, render, check the rendered HTML, submit evidence.
3. In a bash terminal, run `quarto render analysis.qmd`; confirm the rendered HTML auto-refreshes. CP/CR.
4. (Optional) Create `data/` directory via `dir.create("data")`. CP/CR.

Replace `XX` placeholders with actual repo names, titles, and knowledge drops.

Keep introductions short. Avoid repeating details that the exercises will teach. If students must read several paragraphs before the first exercise, split the prose with Continue buttons; better yet, cut the prose down.

**Companion-text link.** If a tutorial is the companion to a specific chapter of a book or other text, its **first sentence** should name and link that **exact chapter** — not merely the work as a whole. A reader should be able to click straight through to the matching chapter. (Primer tutorials, for example, open by naming and linking the specific Primer chapter they support, in addition to the book.)

### Topic exercises

- Begin by getting data into the student's project. Often this means asking AI to create `data/`, download a file from a stable URL, and record where it came from.
- Ask for one concrete edit per exercise. Do not micromanage individual R function arguments unless the detail is pedagogically important.
- Render after every meaningful edit. The rendered HTML is the student's feedback loop.
- Use several linked exercises to build an analysis path: inspect the data, notice a pattern or problem, refine the data, make a rough plot, improve the plot, add interpretation.
- The final section should make the rendered page look good enough to publish.
- Before including a visualization exercise, ask what it shows that the preceding table or exercise did not. If the answer is nothing new — the same finding in a different form — cut the exercise and communicate the finding in the table's knowledge drop instead.

### Writing student prompts to AI

Describe the **goal**, not the implementation. Students should tell AI what they want to see — a plot of X by Y, a table of the top 10, the distribution of Z — and let the AI choose the functions. This is how professionals actually work, and it gives students practice evaluating whether the AI's approach is correct.

- **Do not** list functions for students to include in their AI prompt (e.g., "use `geom_histogram()` and `scale_x_log10()`").
- **Do not** dictate the pipe steps or function arguments students should pass to AI.
- **Exception**: it is fine to name one key function when it is the explicit concept being taught (e.g., "use `pivot_longer()` to reshape the data") — but only if that function is the point of the exercise, not just an implementation detail.
- Knowledge drops are the right place to introduce function names after students have already seen the output. Students learn what `geom_smooth()` is by noticing it in AI-generated code, not by being told to ask for it.

### Git commit exercises

Each topic section should end with a dedicated commit exercise placed as the last exercise in that section, after the final plot or analysis output. Do not fold it into another exercise. This per-section cadence is the **default**; a project may override it on the record (the Primer, for instance, commits only after Justice and at the end).

The exercise asks students to commit `analysis.qmd` with a specific descriptive message. Students may use any of three methods — all are acceptable:

- **AI agent**: "Ask AI to stage `analysis.qmd`, commit with the message `"Add X analysis"`, and push to GitHub."
- **VS Code Source Control panel**: click the branch icon in the sidebar, stage the file, enter the message, and sync.
- **Command line**: `git add analysis.qmd && git commit -m "Add X analysis" && git push` in a bash terminal.

The Summary commit exercise (sequence step 3) should say "commit any remaining changes" — by that point the main content is already committed section by section.

Exception: if the tutorial has only one or two topic sections and the Summary's commit would cover all outstanding work, the final topic-section commit may be omitted to avoid asking students to commit twice in quick succession.

### Exercise rhythm

Most exercises should follow this rhythm:

1. **Prompt AI / edit `analysis.qmd`** — students ask their AI agent to add or change something concrete.
2. **Render** — students run `quarto render analysis.qmd` in a bash terminal and inspect the rendered HTML.
3. **Verify** — students submit evidence that they completed the exercise. Use CP/CR only for terminal command-and-response evidence.
4. **Show the expected output** — after students submit and press Continue, show our expected output, answer, plot, tibble, or representative paste.
5. **Knowledge drop** — insert another `###` separator so students press Continue again before seeing the knowledge drop. Then provide a short paragraph that tells students what to notice, teaches domain knowledge, or foreshadows the next exercise.

The canonical loop is:

`prompt AI/edit analysis.qmd -> render -> inspect rendered HTML -> submit evidence -> expected output/answer -> Continue -> knowledge drop -> next exercise`

### Submission evidence

CP/CR means **Copy/Paste the Command/Response**. Use CP/CR only for terminal or R Terminal submissions where students paste both the command they ran and the response they got. Do not add extra wording like "the command and response" or "the terminal output"; that is already implied by CP/CR. Assume students already know what CP/CR means — they learned it in the `vscode.tutorials` infrastructure tutorials — so do **not** explain it in a normal tutorial; just use the shorthand. Put the `CP/CR.` instruction on its own line at the end of the prompt.

For rendered HTML output, say "copy and paste from the HTML" or "copy and paste the table/summary/text from the HTML." Do not call HTML submissions CP/CR.

Use `show_file()` when checking file contents or code: `.gitignore`, the last chunk, a data-analysis pipeline, chart code, or the final QMD state. When checking chart code or a data pipeline, pair `show_file()` with our rendered plot, tibble, or other output so students can compare both the code and its result.

Beyond `show_file()`, R Terminal CP/CR should be rare (per *Student workflow*): reach for it only for the occasional directory-structure or shell check (`list.files()`, `pwd`, `ls`, render messages) that has no place in the QMD. Anything that produces analysis output belongs in a QMD chunk, rendered, and copied from the HTML — not run in the R Terminal.

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

**Structural exploration for downloaded or unfamiliar datasets.** Before moving to plots, add exercises that let students discover the dataset's shape and vocabulary. Three exercises work well together, all done in the QMD and read from the render: `names()` (ask AI to add a chunk that prints the column names, so students have them exactly when prompting); `glimpse()` on a column group of ≤ ~15 columns reveals types and spot-checks values; and `summary()` on key numeric columns catches placeholder values and establishes scale before plotting. Do not use `glimpse()` on the full dataset when it has more than ~15 columns — split by column group first (e.g., `select(starts_with("artist")) |> glimpse()`). These exercises are most valuable for downloaded CSVs and unfamiliar datasets; they are less necessary for well-known built-in datasets where the column structure is self-evident from the first print. Place any "what does this variable mean?" exercise immediately after students first see the column name — not after they have already used it in a summary or plot.

**Categorical comparisons.** For comparisons across categories, scaffold the student's choice rather than prescribing it: first have students count observations per category so they can see what is plentiful, then ask them to choose two categories with enough data to compare (e.g., at least 100 rows each), then build the comparison plot. This gives students practice making an informed analytical decision rather than following a script.

The final artifact should be a published page with a meaningful result about the world, not just a completed worksheet.

For histograms and other distribution plots, usually include the number of observations used in the plot, especially when missing values or filters may exclude rows. Put `n` in the subtitle, caption, or nearby knowledge drop so readers know whether they are seeing the full dataset or only available values.

### Summary exercises (standard sequence)

1. Final `quarto render`, `show_file("analysis.qmd")`, submit evidence.
2. `quarto publish gh-pages analysis.qmd` in bash Terminal; paste resulting URL.
3. Commit and push any remaining changes; paste GitHub repo URL.

## Question types

### No-answer questions (default)

```r
question_text(NULL,
  answer(NULL, correct = TRUE),
  allow_retry = TRUE,
  try_again_button = "Edit Answer",
  incorrect = NULL,
  rows = 5)
```

Use for evidence-submission questions. Set `rows` to match expected output length.

### Yes-answer questions

```r
question_text(NULL,
  message = "Correct answer text here.",
  answer(NULL, correct = TRUE),
  allow_retry = FALSE,
  incorrect = NULL,
  rows = 6)
```

Use when providing the correct answer. Set `allow_retry = FALSE`.

## Knowledge drops

**Every knowledge drop in a normal tutorial does one of three jobs.** The generic, reusable drops the infrastructure tutorials lean on — *"Professionals keep their data science work in the cloud because laptops fail,"* *"the tidyverse is a family of packages…"*, *"QMD World and R World are not the same"* — belong **only** in `vscode.tutorials`. In a normal tutorial, a knowledge drop must instead:

1. **Make a key point from the companion book chapter** (when there is one). Students usually don't read the chapter, so the tutorial is where we *pull out* the points they most need to carry away.
2. **Talk about the data** the tutorial works with — its source, its units of observation, how it was collected, a quirk or limitation worth knowing.
3. **Comment on what the most recent command displayed** — point out something in the plot or table the student just produced that they probably didn't notice, or that guides them toward the next question.

If a drop isn't doing one of these three, cut it. Do **not** reach for a canned infrastructure lesson to fill the slot.

- Every exercise must have a knowledge drop after submission, and it must be one of the three kinds above. This holds even when the exercise is mostly setup, committing, publishing, or verification — for those, pull a key point from the chapter or say something about the data, rather than narrating the mechanics.
- Usually one short paragraph; one or two sentences is often enough.
- Place after students submit an exercise and after the expected output/answer. The usual pattern is question chunk, `###`, expected output/answer, `###`, knowledge drop.
- Place before the question only when context is needed to answer.
- Reference our example output when the test chunk displays a plot, tibble, or summary. When the test chunk reveals specific numbers, use them — "496 zero values (nearly 5%)" rather than "many zero values." Concrete numbers make knowledge drops credible and easier for students to verify against their own output.
- Tell students what to notice in their rendered output, explain why it matters, or identify the next natural data-science move.
- In the age of AI, knowledge drops should emphasize concepts students need to steer and audit AI-generated work: data shape, units of observation, missingness, filtering, variable meaning, plot interpretation, domain context, and whether a result supports the claim being made.
- When AI handles syntax or transformations under the hood, name the concept and explain it in plain language after students see the result. Students do not need to memorize syntax immediately, but they do need to recognize what AI did and why it matters.
- Do not use recycled/default knowledge drops in post-infrastructure tutorials. Save repeated infrastructure lessons for the `vscode.tutorials` infrastructure tutorials; later tutorials need knowledge drops tied to the current exercise, current data, or current infrastructure issue.
- Use knowledge drops to teach the data science ecosystem for the tutorial's area: gold-standard data sources, common measures, important packages, file formats, APIs, data-quality issues, and standard patterns analysts look for.
- Name packages and functions students will encounter in AI-generated code — so they can recognize and evaluate them, not so they can dictate them in prompts.
- No road signs ("In the next section..."). Teach something real.
- No rhetorical questions.

## Code chunk conventions

- Label format: `section-name-N` and `section-name-N-test` (e.g., `billboard-3`, `billboard-3-test`).
- Run `tutorial.helpers::check_current_tutorial()` after adding/deleting exercises to renumber everything.
- Use `tutorial.helpers::make_exercise()` to add new exercises with correct numbering.
- `echo = FALSE` everywhere (set globally in setup chunk via `knitr::opts_chunk$set(echo = FALSE)`).
- Set `knitr::opts_chunk$set(out.width = '90%')` in setup for consistent image sizing.
- Avoid exercise code chunks in post-infrastructure tutorials. Use question chunks for evidence submissions and test chunks for our example output.
- Do **not** include the `copy-code-chunk` (copy-button) child document. Its only purpose is to add a copy button to exercise code chunks, and these tutorials have none. The `info-section` and `download-answers` child documents stay.

## Test chunk output

Test chunk output should be scannable in a few seconds. If it scrolls, it is too long. Apply these fixes before committing:

**Wide tibbles (many columns)** — `glimpse()` prints one line per column; on a 79-column dataset that is 81 lines. Print the tibble directly instead (`billboard`, not `glimpse(billboard)`): tibbles auto-truncate to 10 rows and summarise extra columns on one line. Reserve `glimpse()` for narrow datasets (≤ ~15 columns) where the column-by-column view is genuinely useful.

**CSV column-spec messages** — `read_csv()` prints a column-spec block (one line per column) before the tibble. Suppress it with `show_col_types = FALSE`:

```r
read_csv("../../extdata/r4ds-1/music.csv", show_col_types = FALSE)
```

**Long model or computation output** — wrap with `suppressMessages()` or redirect progress with `refresh = 0, silent = 2` (for **brms**). Only show the final object students need to check.

**Plots** — always fine; they produce a single visual, not scrolling text.

**The rule of thumb**: if the rendered test chunk output is taller than a laptop screen, shorten it.

## Setup chunk requirements

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

## Data handling

- Never depend on a fragile third-party URL at tutorial run time.
- Keep stable source copies for student downloads under `inst/extdata/<tutorial>/` when practical, and document the original source.
- Students usually create their own `data/` directory inside their project and download or copy data there.
- In later tutorials, prefer asking AI to download a file from a stable URL and put it in `data/` over teaching students exact `download.file()` syntax, unless that syntax is the point of the exercise.
- Avoid `inst/tutorials/<name>/data/` for new post-infrastructure tutorials unless there is a specific learnr runtime reason.
- Do not download from the web during tutorial compile/run. If a test chunk needs data, load a small stable copy from the package.
- For large data, create smaller teaching files and use lightweight evidence submissions rather than heavy test computations.
- If a teaching dataset is incomplete, sampled, capped, or otherwise artificial, do not hide that fact. Build an exercise path that lets students discover the limitation and reason about how it affects interpretation.

## Authoring with AI agents

When changing or creating subject-area tutorials, use Claude/Gemini iteratively:

- Ask the agent to inspect the existing tutorial and propose changes before editing.
- Correct the agent's plan when it misses project rules, especially the render + Live Server workflow, no post-infrastructure exercise code chunks, data handling, and knowledge drops.
- Make one section correct first. Render it, inspect the result, and revise before applying the same pattern elsewhere.
- Ask the agent what it will do in the next section before it edits.
- Keep expected output, prose, prompts, knowledge drops, and displayed example plots in sync when the data or analysis changes.

## Images

- Store in `images/` directory alongside `tutorial.Rmd`.
- Include with `knitr::include_graphics("images/example.png")` in an unnamed chunk.
- Requires `library(knitr)` in the setup chunk.

## Displaying code verbatim in tutorials

Use `<pre><code>` wrappers (not four backticks) to display R code chunks verbatim inside tutorial text:

```
<pre><code>```{r}
1 + 1
```</code></pre>
```

When showing multiple commands for students to copy/paste, make sure they render as separate lines. Do not let Markdown collapse separate commands into one paragraph.

## Checking a tutorial

1. **Quick syntax check**: `rmarkdown::render("inst/tutorials/name/tutorial.Rmd")` — open resulting HTML in browser.
2. **Full check**: `devtools::check()` (`Cmd/Ctrl + Shift + E`) — must pass before any PR.
3. **Student perspective**: `devtools::install()` then `learnr::run_tutorial("tutorial_name", "<your package>")` (e.g. `"misc.tutorials"` or `"primer.tutorials"`).

`devtools::check()` validates the default `tutorial_template` child documents. Keep `info-section` and `download-answers`; do not remove or alter them. The `copy-code-chunk` child is no longer used (see *Code chunk conventions*) — if `check()` still flags its absence, that check in `tutorial.helpers` is what needs updating, since the copy button has no role once there are no exercise code chunks.

## Formatting conventions

- Keyboard input and inline code: `backticks`
- Package names in prose: bolded and linked to the package's gold-standard documentation site, usually the official package website (e.g. **[ggplot2](https://ggplot2.tidyverse.org/)**, **[dplyr](https://dplyr.tidyverse.org/)**). Do not link package names inside code.
- Function names always include parentheses: `read_csv()`, not `read_csv`
- Section/topic titles: sentence case
- Document titles (the `analysis.qmd` title) use **Title Case** — e.g. `"Sampling"`, not `"sampling"`. (Distinct from section/topic headings, which stay sentence case.)
- Terminal names: `R Terminal` and `bash terminal`
- Abbreviation: CP/CR = Copy/Paste the Command/Response

## DESCRIPTION

Any library loaded in a tutorial must appear under `Imports` or `Suggests` in `DESCRIPTION`. `devtools::check()` on GitHub Actions will fail if a package is `library()`-ed but not listed.

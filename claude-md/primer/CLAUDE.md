# CLAUDE.md — Preceptor's Primer authoring guide (index)

> **Primer tutorials follow the base tutorial guide — [`../tutorials/CLAUDE.md`](../tutorials/CLAUDE.md) — by default.** They are "normal" tutorials; that guide is the default contract and the source of truth for everything common to all tutorials. **Read it first.** This Primer guide adds only what is *specific* to the Primer, or records an **explicit override** (never a silent one). (Book chapters are a different artifact — the base guide is tutorial-only; see [`chapters.md`](chapters.md).)

This file is the working reference for creating data science education artifacts. The first artifact is a chapter in the textbook *Preceptor's Primer for Bayesian Data Science: Using the Cardinal Virtues for Inference*. The second is a matching learnr tutorial. Every chapter has an associated tutorial and vice versa. The third is a class exercise covering similar material. The file is addressed to Claude. David Kane is the author; Claude is the co-author he collaborates with to produce new material.

The goal is that this guide, **together with the base tutorial guide**, is the reference either of us needs when starting work on a new chapter/tutorial pair or class exercise. The guide is **split into parts** (this file is the index); read only the parts a task needs — see the map below.

## Base tutorial guide (read this first)

Primer tutorials are "normal" tutorials, so they inherit the **base tutorial guide** — the default contract for authoring any data science tutorial in this ecosystem, at `claude-md/tutorials/CLAUDE.md` in the PPBDS/ai-rules repo. **Before writing any Primer tutorial, read in and accept that guide.** It is the default; this guide does not repeat it.

The base guide is the source of truth for everything common to all such tutorials:

- the AI-era philosophy — students create artifacts by prompting an AI agent, not by typing code into learnr exercise chunks;
- the workflow — student work lives in `analysis.qmd`; they render with `quarto render` in a bash Terminal and view the result via **Live Server**;
- exercise rhythm, the `question_text()` question types, knowledge-drop discipline, CP/CR and `show_file()` evidence;
- `echo = FALSE`, test-chunk discipline, code-chunk labeling, the setup-chunk skeleton, data handling, and formatting (package names bold+linked, function names with `()`, sentence case).

This guide covers only what is **specific to the Primer**: the Cardinal Virtues structure, the EMH progression, predictive/causal pairing, Preceptor and Population Tables, the canonical definitions, the knowledge-drop library, the master exercise list, and the per-tutorial seed specs.

**Precedence.** On workflow and shared conventions, the base guide wins. On Primer-specific pedagogy, this guide wins. Anywhere the Primer departs from the base guide, that departure must be an **explicit, on-the-record override** — called out as such at the point it occurs — never a silent difference.

## How this guide is organized — read on demand

This index is the entry point. The rest of the guide lives in the part files below. **Read only the parts your current task needs** — you rarely need more than two or three. **Section numbers (`§1`…`§17`) are unchanged across the split**, so every `§N.M` cross-reference inside the parts still resolves: use this map to find which file a `§` lives in.

| Part file | Covers | Read when… |
|---|---|---|
| [`curriculum.md`](curriculum.md) | §1 Project, §8 Spaced repetition | designing or sequencing the curriculum; fixing a tutorial's tier (EMH) and predictive/causal framing |
| [`chapters.md`](chapters.md) | §4 Chapter structure | writing a **book chapter** |
| [`authoring.md`](authoring.md) | §3 file conventions, §5 tutorial structure, §6 question flow, §7 exercise types, §9 AI-mediated exercises, §15 R tooling | building or editing a **tutorial** |
| [`guidance.md`](guidance.md) | §14 cross-cutting author guidance (rounding, visualization house style, LaTeX, package-name formatting, …) | applying the cross-cutting rules while writing exercises |
| [`tables.md`](tables.md) | §10 Preceptor & Population Tables | building a Preceptor or Population Table (`gt`) |
| [`concepts-and-drops.md`](concepts-and-drops.md) | §11 Canonical definitions, §12 Knowledge-drop library | writing definition answers or knowledge drops |
| [`exercise-list.md`](exercise-list.md) | §13 Master exercise list | authoring the exercise **sequence** of a tutorial |
| [`per-tutorial/<id>.md`](per-tutorial/) | §17 seed spec for one tutorial | authoring **that** tutorial — read only its file (e.g. `per-tutorial/08-seguro-popular.md`) |
| [`open-items.md`](open-items.md) | §16 Open items | checking pending TODOs |

**To author a tutorial, the usual path is:** base guide → this index → `curriculum.md` (its tier and framing) → `authoring.md` → `exercise-list.md` → `per-tutorial/<id>.md`, pulling in `tables.md` and `concepts-and-drops.md` as the exercises require. **To write a chapter:** base guide → this index → `chapters.md` → `tables.md` / `concepts-and-drops.md` / the tutorial's `per-tutorial/<id>.md`.

## Curriculum at a glance

The load-bearing framing (full detail in [`curriculum.md`](curriculum.md)):

- **16 chapter/tutorial pairs**: 4 **miscellaneous** (01 Probability, 02 Sampling, 03 Rubin Causal Model, 04 Cardinal Virtues) + 12 **example** tutorials (05–16), each working a real data-science problem through the four Cardinal Virtues.
- **EMH tier is fixed by tutorial number**: 05–08 Easy, 09–12 Medium, 13–16 Hard. Read the number; don't guess.
- **Predictive / causal alternation**, starting predictive at position 1 (tutorial 05). Each example chapter also carries the *paired* opposite-framing question on the same outcome and covariates.
- **Non-parametric (random forest) tutorials come last** (positions 11–12, tutorials 15–16).
- Concepts are **introduced in order** — never quiz a concept the curriculum introduces in a later chapter (base guide, *Don't quiz concepts the student hasn't reached*; Primer schedule in `curriculum.md`).

## 2. Working with David

The authoring of a chapter/tutorial pair is a conversation. Do not try to produce a finished chapter in one shot. The rough protocol:

1. **David picks the topic or gives a pointer.** Example: "Write chapter 9, on logistic regression."
2. **Claude proposes the framing.** Candidate dataset(s), the Imagine-that-you-are scenario, the broad question, a specific narrow question (the QoI), and whether the tutorial (and therefore the primary question) will use a predictive or causal model. Offer two or three options where there is real choice. Also propose the paired question — the opposite framing using the same outcome and covariates.
3. **David picks.** Iterate on the dataset, the unit, the outcome, the treatment (if causal), and a short list of covariates. The same choices govern both the primary and the paired question.
4. **Claude drafts both Preceptor Tables and both Population Tables** as `gt` code — primary question first, paired question second. David reviews and corrects.
5. **Claude drafts the chapter Wisdom section**, then the tutorial Wisdom section. David reviews. Repeat by virtue: Justice, Courage, Temperance.
6. **Claude checks spaced-repetition coverage** against the per-tutorial specifications in §17 and adjusts which recurring questions this tutorial asks.

This protocol is a default; deviate when it makes sense. Where a decision is small and reversible (phrasing of a knowledge drop, which concrete example to use in an exercise), just make it. Where a decision shapes the rest of the chapter (dataset, QoI, functional form), pause and ask.

When you pause to ask, make it easy for David to answer: short list of options, your recommendation, your reasoning. Do not ask open-ended questions when a multiple-choice question will do.

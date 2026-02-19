# Flask Sequence Hyperbook

## What this is

A hyperbook (interactive workbook) companion for the Flask teaching sequence. Not a replacement for live demos or the assignment repos — it's "bumpers in the lane" for students who need sequenced, reference-style materials.

Built with [hyperbook](https://hyperbook.openpatch.org/). Content lives in plain markdown with `:::` directive syntax for interactive elements (collapsibles, tabs, alerts, mermaid diagrams).

## Project structure

- `hyperbook.json` — config, basePath set for GitHub Pages
- `book/` — content organized by week (`01-hello-flask/`, `02-templates/`, ...)
- `glossary/` — auto-linked terms (use `:t[term]` in content)
- `.github/workflows/deploy.yml` — auto-deploys on push to main

Live at: https://mrcarle-asb.github.io/flask-sequence-workbook/

## Source materials

Starter code and assignments live in a parallel repo:
`../GrandProjectVision-FlaskSequence/m27 Flask Sequence/`

Older examples and docs are in:
`../hyperbook/resources/`

The hyperbook should stay aligned with the starter code students actually open, but it is NOT a clone of the README. It's the companion reference.

## Pedagogical framing

- These skills are **table stakes** for the IA — not rubric prep, not assessment-adjacent language
- "If you cannot build a basic CRUD app, you cannot build an IA"
- Week 6 (Flask Plateau) is a dress rehearsal in **complexity**, not a mini version of the academic assessment
- Do NOT bring m26 IA handbook specifics into these hyperbooks — m27 has a significantly different assignment description
- The workbook builds a **Minimum Build Path** toward a median CRUD IA-level project

## Content conventions

- Verb-form function names for routes (`show_tasks`, `list_tasks`, not `tasks`)
- After introducing the `tasks=sample_tasks` naming distinction early in Week 2, switch to `tasks=tasks` for all subsequent examples
- "This is things to DO" — warn students that reading without building doesn't work
- Each week section has: concept pages, common mistakes, check-yourself (collapsible Q&A + practical checklist)
- Use `:::alert{warn}` for "do this, not just read this" reminders
- Use `::::tabs` for before/after or works/broken comparisons

## Current state

- **Week 1 (Hello Flask):** 7 pages, shipped and deployed
- **Week 2 (Templates):** 6 pages + 4 glossary terms, written but not yet pushed
  - TODO before pushing: update m27 starter code (`2-flask-templates/app.py`) to use `tasks=tasks` and verb function names to match the hyperbook
- **Weeks 3-6:** Not yet built

## Week sequence overview

1. Hello Flask — app object, routes, decorators, render_template with static HTML
2. Templates — Jinja2 variables, loops, conditionals, base.html, template inheritance
3. Forms + File I/O — HTML forms, POST routes, CSV read/write (Create in CRUD)
4. Birthdays (side quest) — reinforcement with a different dataset
5. Birthdays continued — Update + Delete
6. Flask Plateau — capstone rebuild, full CRUD from scratch

## Dev commands

```
npx -y hyperbook dev     # local server at localhost:8080
npx -y hyperbook build   # static build to .hyperbook/out/
```

## GitHub

- Org: mrcarle-asb
- Repo: flask-sequence-workbook
- Pages deploy: automatic on push to main via GitHub Actions

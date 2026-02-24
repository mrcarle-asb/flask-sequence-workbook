---
name: "Week 3: Forms & File I/O"
index: 3
---

# Week 3: Forms & File I/O

Until now your app has been read-only. Visitors can look at data, but they can't change anything. This week that changes.

You're going to build a personal task tracker — a form that collects task data and saves it to a file. Restart the server and your tasks are still there. That's the difference between an app and a demo.

:::alert{warn}
Same rule as always: **build it, run it, see it work.** The app in this week's starter code already runs — visit `/display` before writing a single line and see what you're building toward.
:::

## What you already know

- Flask routes, `render_template()`, `base.html` inheritance
- Jinja2 variables, loops, and conditionals
- `url_for()` — used in Week 2 templates

## What's new this week

- HTML forms: `<form>`, `<input>`, `<textarea>`, `<select>`
- HTTP methods: GET vs POST
- `request.form` — reading what the user submitted
- The `csv` library: `DictReader` and `DictWriter`
- `redirect()` and the POST-redirect-GET pattern

## Why it matters

Your app now has two new capabilities it will keep for the rest of the sequence:

**Interactive** — users can add data, not just read it.

**Persistent** — data survives a server restart. This is the beginning of a data layer. Right now it's a CSV file. Later it will be a database. The concept is the same: write data somewhere it lives between requests.

By the end of this week, you have a working task tracker. It can't edit or delete entries yet — that comes later. But it does the one thing that matters: it records work. That's enough to be genuinely useful.

:::alert{info}
Read the pages in this section in order. Each one introduces new syntax that the next page builds on.
:::

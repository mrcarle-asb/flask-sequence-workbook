---
name: "Week 4: Birthdays & SQL"
index: 4
---

# Week 4: Birthdays & SQL

Welcome to the gaiden. This week you step away from the Record of Tasks app and build something different — a birthday tracker. Same Flask skills, new data layer.

The big change: instead of reading and writing a CSV file, your app talks to a **database** using **SQL queries**. The database handles searching, sorting, and storage. You describe what you want; it figures out how to get it.

:::alert{warn}
This is still a build-it-to-learn-it week. Run the starter code first. The form and table are already in the HTML — your job is to wire up the Python that makes them work.
:::

## What you already know

- Flask routes, `render_template()`, template inheritance
- HTML forms, `method="POST"`, `request.form`
- `redirect()` and `url_for()`
- Persisting data to a file (CSV)

## What's new this week

- **SQL** — a language for querying databases (`SELECT`, `INSERT`)
- **SQLite** — a lightweight database stored in a single `.db` file
- **The CS50 SQL library** — a Python wrapper that lets you run SQL with `db.execute()`
- **Database schemas** — how a table's structure is defined (columns, types, constraints)

## Why it matters

In Week 3 you stored tasks in a CSV. It worked. But you also felt the friction: reading the whole file to display data, appending rows with careful column ordering, no way to enforce that a field exists or has the right type.

A database removes that friction. One line of SQL replaces the open-read-loop-close pattern. And because SQL is a standard language — not a Python-specific tool — you'll use it again in the IB exam (Paper 2) and in any backend work after this course.

By the end of this week you have a working birthday tracker that can display entries and add new ones. It can't edit or delete yet — that comes in Week 5.

:::alert{info}
If you want deeper background on SQL and Flask, CS50's [Week 9 lecture notes](https://cs50.harvard.edu/x/notes/9/) cover the same territory from a different angle. This hyperbook focuses on what you need for the assignment.
:::

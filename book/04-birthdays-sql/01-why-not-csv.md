---
name: Why Not CSV Forever?
index: 1
---

# Why Not CSV Forever?

Your Week 3 task tracker works. Data persists. Forms submit. But think about what the code actually does every time someone visits `/display`:

1. Open the CSV file
2. Read **every row** into a list
3. Loop through the list to build the page
4. Close the file

That's fine for 10 rows. What about 10,000? You're loading the entire file into memory just to show a table. And if you wanted to show only one project's tasks, you'd still read everything and then filter in Python.

Now think about what you *can't* easily do with a CSV:

::::tabs{id="csv-limits"}

:::tab{title="Find one record"}
**CSV:** Open the file, read every row, check each one, return the match.

```python
with open("tasks.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        if row["project_title"] == "Birthday Tracker":
            # found it
```

**SQL:** One line. The database does the searching.

```python
rows = db.execute("SELECT * FROM tasks WHERE project_title = ?", "Birthday Tracker")
```
:::

:::tab{title="Enforce required fields"}
**CSV:** Nothing stops you from writing a row with an empty title. The file accepts anything.

**SQL:** The schema can declare a column `NOT NULL`. The database rejects the row if the field is missing — before it ever gets stored.
:::

:::tab{title="Delete one row"}
**CSV:** Read the entire file, skip the row you don't want, write everything else back. Hope you don't corrupt the file in the process.

**SQL:** One line.

```python
db.execute("DELETE FROM tasks WHERE id = ?", task_id)
```
:::

::::

## What a database gives you

A :t[database] is structured storage with a query language. Instead of writing code to read, search, and manipulate files, you write **queries** — short statements that describe what you want. The database engine handles the how.

For this week, the database is **SQLite** — a database stored in a single file (`birthdays.db`). No server to install, no network connection needed. It just works like a smarter file.

The query language is **SQL** (Structured Query Language). You'll learn two operations this week:

| You want to... | SQL operation |
|----------------|---------------|
| Read data      | `SELECT`      |
| Add data       | `INSERT`      |

That's it. Two queries. The rest of Flask — routes, templates, forms, redirect — works exactly the same as Week 3.

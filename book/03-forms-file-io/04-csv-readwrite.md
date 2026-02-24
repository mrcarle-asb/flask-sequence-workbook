---
name: Reading and Writing CSV Files
index: 4
---

# Reading and Writing CSV Files

You need to store tasks between server restarts. A CSV file is the right tool for now: plain text, opens in a spreadsheet, no setup required.

## Why not just write the file yourself?

You could open a file and write comma-separated values manually. But what happens when a description contains a comma? Or a newline? Your homemade parser breaks. This problem is exactly solved by the standard library.

```python
import csv
```

No pip install. It's built into Python.

## Reading: `csv.DictReader`

`DictReader` reads each row as a dictionary, using the first row of the file as the keys:

```python
tasks = []
with open("tasks.csv", "r") as f:
    reader = csv.DictReader(f)
    for row in reader:
        tasks.append(row)
```

Each `row` is a dict: `{"datetime": "2025-10-07", "project_title": "...", "title": "...", ...}`. This is exactly what Jinja's `{{ task.title }}` expects.

:::alert{info}
The starter code's `/display` route already does this. Read it carefully — your POST handler writes rows in the same structure that this reader expects.
:::

## Writing: `csv.DictWriter`

`DictWriter` writes dictionaries as rows. You define the column order with `fieldnames`:

```python
with open("tasks.csv", "a", newline="") as f:
    fieldnames = ["datetime", "project_title", "title", "description", "status", "next_steps"]
    writer = csv.DictWriter(f, fieldnames=fieldnames)
    writer.writerow({
        "datetime": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        "project_title": project_title,
        "title": title,
        "description": description,
        "status": status,
        "next_steps": next_steps,
    })
```

## The mode that destroys your data

::::tabs{id="mode"}

:::tab{title="\"a\" — Append (correct)"}
```python
with open("tasks.csv", "a", newline="") as f:
```
Opens the file and positions the write cursor at the **end**. Existing rows are untouched. New rows are added below.
:::

:::tab{title="\"w\" — Write (data loss)"}
```python
with open("tasks.csv", "w", newline="") as f:
```
Opens the file and **deletes everything in it** before writing. Every form submission wipes all previous tasks. This is the most painful mistake in Week 3 — you don't notice until you've submitted several tasks and suddenly they're all gone.
:::

::::

:::alert{warn}
Use `"a"` for append. The seed data in `tasks.csv` even documents this mistake — read the last two rows.
:::

## The `newline=""` argument

```python
with open("tasks.csv", "a", newline="") as f:
```

Without `newline=""`, Python adds an extra blank line after every row on Windows (and sometimes Mac). Your CSV will have alternating data rows and empty rows, and `DictReader` will get confused. Always include it when writing CSV.

## The tool acquisition pattern

Walk through the reasoning:
1. You need to store structured data in a text file.
2. You could split each line by commas — but commas can appear inside fields.
3. Surely someone has solved this.
4. `import csv` — standard library, no install, handles quoting automatically.

This pattern — "I have a structured problem, let me find a library" — comes up constantly. The answer is almost always already in the standard library or a one-line pip install.

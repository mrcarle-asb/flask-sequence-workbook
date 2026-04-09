---
name: "SQL Queries: SELECT and INSERT"
index: 3
---

# SQL Queries: SELECT and INSERT

You need two SQL operations this week: one to read data, one to add data. Both go through `db.execute()`.

## Setup: the CS50 SQL library

At the top of `app.py`:

```python
from cs50 import SQL

db = SQL("sqlite:///birthdays.db")
```

`cs50` is a third-party library (already installed in Codespaces). It wraps SQLite with a simpler interface. The important thing it does: **it returns query results as a list of dictionaries**.

That should sound familiar.

---

## SELECT — reading data

```python
birthdays = db.execute("SELECT * FROM birthdays")
```

This returns **a list of dictionaries**, one per row:

```python
[
    {"id": 1, "name": "Alice", "month": 3, "day": 14},
    {"id": 2, "name": "Bob", "month": 7, "day": 22},
    {"id": 3, "name": "Charlie", "month": 11, "day": 5},
    ...
]
```

:::alert{info}
This is the same shape as `csv.DictReader` — a list of dictionaries keyed by column name. Your Jinja2 loops work exactly the same way:

```html
{% for birthday in birthdays %}
    <td>{{ birthday.name }}</td>
{% endfor %}
```

In Week 3, the list came from reading a CSV file. Now it comes from a SQL query. The template doesn't know the difference.
:::

### SELECT with a filter

To get only some rows, add a `WHERE` clause:

```python
rows = db.execute("SELECT * FROM birthdays WHERE month = ?", month)
```

The `?` is a **placeholder**. The CS50 library substitutes the value of `month` safely. You don't need `WHERE` for this assignment, but you'll use it in Week 5.

---

## INSERT — adding data

```python
db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)
```

This adds one row to the table. Break it down:

- `INSERT INTO birthdays` — which table
- `(name, month, day)` — which columns (not `id` — that's automatic)
- `VALUES(?, ?, ?)` — one placeholder per column
- `name, month, day` — the Python variables whose values fill the placeholders, in order

After this runs, the new row is in the database immediately. No need to close a file or flush a buffer.

---

## The `?` rule

::::tabs{id="placeholders"}

:::tab{title="Correct — use ?"}
```python
db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)
```
The library handles escaping. Safe from injection.
:::

:::tab{title="WRONG — string concatenation"}
```python
db.execute(f"INSERT INTO birthdays (name, month, day) VALUES('{name}', {month}, {day})")
```
A user who types `'; DROP TABLE birthdays; --` as their name will delete your entire table.
:::

::::

**Always use `?` for user input.** Never paste variables directly into the SQL string. The `?` placeholders keep your app safe from SQL injection attacks.

This is not a hypothetical risk. It's one of the most common web security vulnerabilities, and it has a name: [SQL injection](https://xkcd.com/327/).

---

## Reference

| You want to... | Code | Returns |
|----------------|------|---------|
| Read all rows | `db.execute("SELECT * FROM birthdays")` | List of dicts |
| Read some rows | `db.execute("SELECT * FROM birthdays WHERE month = ?", month)` | List of dicts (filtered) |
| Add a row | `db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)` | The new row's id |

Two queries. That's all you need this week.

:::alert{info}
The CS50 SQL library documentation has the full list of what `db.execute()` returns for each type of query: [CS50 Library for Python — cs50.SQL](https://cs50.readthedocs.io/libraries/cs50/python/#cs50.SQL). The two that matter now:

- **SELECT** returns a list of `dict` objects (one per row) — this is what your Jinja2 loops consume
- **INSERT** returns the primary key of the new row (or `None`)

You'll see DELETE and UPDATE in that list too — those come in Week 5.
:::

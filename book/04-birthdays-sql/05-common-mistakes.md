---
name: Common Mistakes
index: 5
---

# Common Mistakes

## Pasting variables into SQL strings

```python
# WRONG — SQL injection vulnerability
db.execute(f"INSERT INTO birthdays (name, month, day) VALUES('{name}', {month}, {day})")

# CORRECT — use ? placeholders
db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)
```

The `?` placeholders are not optional style. They prevent SQL injection — a real attack where malicious input can delete or expose your data. Always use them.

---

## Uncommenting the loop too early

If you uncomment the `{% for birthday in birthdays %}` loop in the template **before** your route passes `birthdays` to the template, you'll get an error:

```
jinja2.exceptions.UndefinedError: 'birthdays' is not defined
```

Fix: complete Step 1 (the `db.execute` query and `render_template` with `birthdays=birthdays`) before uncommenting the loop in Step 2.

---

## Expecting the database to reset

After adding a few test birthdays, you might want to start fresh. But stopping and restarting the server doesn't reset `birthdays.db` — that's the whole point of a database.

If you need to reset the sample data, run `init_db.py` again:

```
python init_db.py
```

This recreates the table and reinserts the 5 sample rows.

---

## Confusing the list with a single dict

`db.execute("SELECT * FROM birthdays")` returns a **list** of dictionaries — even if there's only one row. If you try to use the result directly as a dict, you'll get unexpected behavior.

```python
# This is a list, not a dict
birthdays = db.execute("SELECT * FROM birthdays")

# To get the first row:
first = birthdays[0]  # {"id": 1, "name": "Alice", ...}
```

For this assignment you always pass the full list to the template and let Jinja2 loop over it, so this rarely comes up. But it matters when you start using `WHERE` to find specific rows.

---

## `request.form["name"]` vs `request.form.get("name")`

Both work for reading form data. The difference:

- `request.form["name"]` raises a `KeyError` if the field is missing
- `request.form.get("name")` returns `None` if the field is missing

The starter code's TODO comments suggest `.get()` (matching CS50's style). Either works here because the HTML form has `required` on all fields — the browser won't submit without them. But `.get()` is the safer default.

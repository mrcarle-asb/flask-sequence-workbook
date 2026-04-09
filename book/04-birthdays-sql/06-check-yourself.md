---
name: Check Yourself
index: 6
---

# Check Yourself

## Concepts

:::collapsible{title="What does db.execute(\"SELECT * FROM birthdays\") return?"}
A **list of dictionaries**. Each dictionary is one row, with column names as keys:

```python
[
    {"id": 1, "name": "Alice", "month": 3, "day": 14},
    {"id": 2, "name": "Bob", "month": 7, "day": 22},
    ...
]
```

This is the same shape as what `csv.DictReader` gave you in Week 3 — a list of dicts. Your Jinja2 loops work exactly the same way.
:::

:::collapsible{title="Why use ? instead of f-strings in SQL queries?"}
To prevent **SQL injection**. If you paste user input directly into a SQL string, a malicious user can craft input that runs arbitrary SQL commands — like deleting your entire table.

The `?` placeholders let the CS50 library handle escaping safely. This is not a style preference; it's a security requirement.
:::

:::collapsible{title="Why did we uncomment the template loop after writing the query?"}
Because the template tries to loop over a variable called `birthdays`. If the route doesn't pass that variable to `render_template()`, Jinja2 raises an `UndefinedError`. The variable has to exist before the template can use it.
:::

:::collapsible{title="What happens to the database when you restart the server?"}
Nothing. The database file (`birthdays.db`) stays on disk. All the birthdays you added are still there. This is the point — persistence means data survives a restart.

If you need to reset the sample data, run `python init_db.py` again.
:::

:::collapsible{title="How is this different from the CSV approach in Week 3?"}
In Week 3, your code opened a file, read every row, and parsed it. To add a row, your code opened the file again and appended a line.

With SQL, you write a query that describes what you want (`SELECT * FROM birthdays`), and the database handles the reading, searching, and writing. You don't manage the file directly — the database engine does.

The result in Python is the same shape (a list of dicts), but the mechanism underneath is fundamentally different.
:::

---

## Practical checklist

Before you submit, verify each of these:

- [ ] The app runs without errors in the terminal
- [ ] The table shows all 5 sample birthdays (Alice, Bob, Charlie, Diana, Edward)
- [ ] You can add a new birthday through the form and it appears in the table
- [ ] The new birthday survives a server restart (stop the server, start it again, refresh)
- [ ] Your SQL queries use `?` placeholders, not f-strings or string concatenation

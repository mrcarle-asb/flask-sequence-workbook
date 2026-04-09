---
name: Completing the Assignment
index: 4
---

# Completing the Assignment

The starter code has the HTML done — a form for adding birthdays and a table for displaying them. Your job is to write the Python that connects them to the database.

:::alert{warn}
Do not start writing code until you've done Step 0. The template and the existing code tell you exactly what shape the data needs to be.
:::

## Step 0: Run the app and read the code

Run `flask run --debug` and visit the app. You'll see a form with three fields (Name, Month, Day) and an empty table below it. The form submits but nothing happens — the POST handler is a `pass`.

Now read `app.py`. It's short. Note:

- `db = SQL("sqlite:///birthdays.db")` — the database connection, created once at the top
- The `index()` route handles both GET and POST on the same URL (`/`)
- The POST block has TODOs — that's where you'll add the insert
- The GET section (after the `if` block) has TODOs — that's where you'll query and pass data to the template

Then open `templates/index.html`. The Jinja2 loop that displays birthdays is **commented out** with `<!-- -->`. You'll uncomment it after the query is working.

---

## Step 1: Complete the GET route

In `app.py`, find the section after the `if` block — the part that runs on GET requests. Replace the two TODO lines with:

```python
birthdays = db.execute("SELECT * FROM birthdays")
return render_template("index.html", birthdays=birthdays)
```

This queries all rows from the database and passes them to the template as a list of dictionaries.

---

## Step 2: Uncomment the template loop

Open `templates/index.html`. Find the `<!-- -->` comment markers wrapping the `{% for %}` loop inside `<tbody>`. Remove the `<!--` before the loop and the `-->` after it.

The loop should now look like this (no comment markers):

```html
<tbody>
    {% for birthday in birthdays %}
    <tr>
        <td>{{ birthday.name }}</td>
        <td>{{ birthday.month }}</td>
        <td>{{ birthday.day }}</td>
    </tr>
    {% endfor %}
</tbody>
```

:::alert{warn}
Do this step **after** Step 1. If the template tries to loop over `birthdays` but the route doesn't pass that variable, you'll get a Jinja2 error. The variable must exist before the template can use it.
:::

---

## Step 3: Test the display

Refresh the page. You should see the 5 sample birthdays in the table:

| Name    | Month | Day |
|---------|-------|-----|
| Alice   | 3     | 14  |
| Bob     | 7     | 22  |
| Charlie | 11    | 5   |
| Diana   | 1     | 30  |
| Edward  | 9     | 17  |

If the table is empty, check that your `db.execute` line is before the `return render_template` line, and that you're passing `birthdays=birthdays`.

---

## Step 4: Complete the POST route

In `app.py`, find the `if request.method == "POST"` block. Replace `pass` with:

```python
if request.method == "POST":
    name = request.form.get("name")
    month = request.form.get("month")
    day = request.form.get("day")

    db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)

    return redirect(url_for("index"))
```

This reads the three form fields, inserts a new row into the database, and redirects back to the home page — the same POST-redirect-GET pattern from Week 3.

---

## Step 5: Test adding and persistence

1. Type a name, month, and day into the form. Click **Add**.
2. The page should reload and your new birthday appears in the table.
3. **Stop the server** (Ctrl+C in the terminal).
4. Start it again (`flask run --debug`).
5. Refresh the page — your birthday is still there.

That's persistence. The database file (`birthdays.db`) stores your data between server restarts, just like `tasks.csv` did in Week 3 — but now the database handles the reading and writing for you.

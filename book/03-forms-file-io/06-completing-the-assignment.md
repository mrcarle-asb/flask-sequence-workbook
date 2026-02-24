---
name: Completing the Assignment
index: 6
---

# Completing the Assignment

You have the concepts. Now apply them. Your starter code has a working display and a stub input form. Your job is two things: build the form in `input.html`, and complete the POST handler in `app.py`.

:::alert{warn}
Do not start writing until you've done Step 0. Reading the working code first is not optional.
:::

## Step 0: Study the working display

Run the app and visit `/display`. You should see a table of pre-loaded tasks from `tasks.csv`.

Now read `app.py` carefully. For the `/display` route, trace the whole flow:

- Where does it open `tasks.csv`?
- How does `DictReader` turn the file into a list?
- How does that list get to `display.html`?
- What does `display.html` do with `task.status` in the `{% if %}` block?

Then look at `/display/<project_title>`. It's the same template but with a filter. Note that it passes `project_title` as a second argument to `render_template` — `display.html` uses that to change the heading.

Once you can trace the full read path, you understand the shape of the data you need to write.

---

## Step 1: Build the form in `input.html`

Open `templates/input.html`. The `<datalist>` is already there. Build your `<form>` around it.

Your form needs:
- `action="/input"` and `method="POST"` on the `<form>` tag
- A text input for `project_title` with `list="project-suggestions"` to connect it to the datalist
- Text inputs for `title` and `next_steps`
- A `<textarea>` for `description`
- A `<select>` for `status` with three `<option>` values: `In Progress`, `Blocked`, `Done`
- A submit button

```html
<form action="/input" method="POST">

    <label for="project_title">Project:</label>
    <input type="text" name="project_title" id="project_title" list="project-suggestions">

    <label for="title">Title:</label>
    <input type="text" name="title" id="title">

    <label for="description">Description:</label>
    <textarea name="description" id="description"></textarea>

    <label for="status">Status:</label>
    <select name="status" id="status">
        <option value="In Progress">In Progress</option>
        <option value="Blocked">Blocked</option>
        <option value="Done">Done</option>
    </select>

    <label for="next_steps">Next Steps:</label>
    <input type="text" name="next_steps" id="next_steps">

    <button type="submit">Save Task</button>
</form>
```

**Test:** Visit `/input`. The form should render with all five fields and the submit button. The project_title field should show autocomplete suggestions from the existing CSV data.

---

## Step 2: Complete the POST handler in `app.py`

Open `app.py`. Find the `if request.method == "POST":` block — it's all TODO comments. Replace the `pass` with working code:

```python
if request.method == "POST":
    project_title = request.form["project_title"]
    title         = request.form["title"]
    description   = request.form["description"]
    status        = request.form["status"]
    next_steps    = request.form["next_steps"]

    with open("tasks.csv", "a", newline="") as f:
        fieldnames = ["datetime", "project_title", "title", "description", "status", "next_steps"]
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writerow({
            "datetime":      datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "project_title": project_title,
            "title":         title,
            "description":   description,
            "status":        status,
            "next_steps":    next_steps,
        })

    return redirect(url_for("display"))
```

**Test:** Submit a task through your form. You should land on `/display` and see your new row at the bottom of the table.

Open `tasks.csv` directly and confirm the row is there. Then restart the server and visit `/display` again — the row should still be there. That's persistence.

---

## Step 3: Verify the filtered view

On `/display`, click one of the project name links. You should see only that project's tasks, with a heading showing the project name and a "← All tasks" link.

Try typing your project name directly into the URL bar: `/display/Your Project Name` (with `+` for spaces: `/display/Your+Project+Name`).

---

## Step 4 (Stretch): Required fields and direct URL

Add `required` to your form inputs:

```html
<input type="text" name="title" id="title" required>
```

The browser will block submission and show a validation message if that field is empty. No Python needed — this is built into HTML.

Then try navigating directly to `/display/Task+Tracker` in the URL bar to see the seed data filtered by project. This is the same mechanism that will power any filtered view you build.

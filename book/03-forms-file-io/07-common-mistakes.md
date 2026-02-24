---
name: Common Mistakes
index: 7
---

# Common Mistakes

These are the mistakes almost everyone makes in Week 3. Most of them fail silently, which makes them particularly frustrating.

## Missing `name=` on a form input

```html
<!-- WRONG — Flask can't find this field -->
<input type="text" id="title">

<!-- RIGHT -->
<input type="text" name="title" id="title">
```

`request.form` is keyed by `name`, not `id`. Without `name`, the field doesn't exist in Flask's view. You'll get a `KeyError` when Python tries to read it, or an empty value if you used `.get()`. The form will look fine — the bug is invisible until submission.

## Missing `method="POST"` on the form

```html
<!-- WRONG — defaults to GET, form data doesn't reach request.form -->
<form action="/input">

<!-- RIGHT -->
<form action="/input" method="POST">
```

Without `method="POST"`, the browser sends a GET request. Form data goes into the URL as query parameters, not the request body. `request.form` will be empty. The route's GET handler runs instead of the POST handler, and the form just re-renders itself.

## Missing `methods=["GET", "POST"]` on the route

```python
# WRONG — Flask only accepts GET by default
@app.route("/input")
def input_task():
    ...

# RIGHT
@app.route("/input", methods=["GET", "POST"])
def input_task():
    ...
```

Without the `methods=` list, Flask returns `405 Method Not Allowed` when the form is submitted. You'll see the error in the browser and a traceback in the terminal.

## Using `"w"` mode instead of `"a"` when writing the CSV

```python
# WRONG — deletes all existing rows before writing
with open("tasks.csv", "w", newline="") as f:

# RIGHT — appends the new row to the end
with open("tasks.csv", "a", newline="") as f:
```

`"w"` (write) truncates the file to zero bytes before writing. Every form submission wipes your entire task history. You won't notice until you've submitted a few tasks, visited `/display`, and found only one row. Check `tasks.csv` directly to confirm whether your rows are actually being kept.

## Missing `newline=""` when opening for CSV writing

```python
# WRONG — extra blank lines appear between rows on some systems
with open("tasks.csv", "a") as f:

# RIGHT
with open("tasks.csv", "a", newline="") as f:
```

Without `newline=""`, the csv module's line terminator and Python's own newline handling combine to insert a blank line after every row. `DictReader` will include these blank rows, and you'll see empty rows appearing in your table.

## Rendering a template after POST instead of redirecting

```python
# WRONG — pressing refresh re-submits the form
if request.method == "POST":
    # ... save data ...
    return render_template("display.html", tasks=tasks)

# RIGHT — redirect breaks the cycle
if request.method == "POST":
    # ... save data ...
    return redirect(url_for("display"))
```

If you render directly after POST, the browser's "last request" was a POST. Refreshing the page asks "resend this POST?" and submitting again creates a duplicate row. Use `redirect()` so the browser's last request becomes a GET.

## Missing `list=` on the project_title input

```html
<!-- WRONG — datalist exists but input isn't connected to it -->
<input type="text" name="project_title" id="project_title">

<!-- RIGHT -->
<input type="text" name="project_title" id="project_title" list="project-suggestions">
```

The `list` attribute value must match the `id` of the `<datalist>` exactly. Without it, the autocomplete suggestions won't appear — but the field still works, it's just not helpful.

---
name: Reading Form Data
index: 3
---

# Reading Form Data

When a user submits a form, Flask collects all the field values into an object called `request.form`. It works like a dictionary: the keys are the `name` attributes from your HTML inputs, and the values are whatever the user typed.

## The mapping

```html
<!-- input.html -->
<input type="text" name="title" id="title">
<textarea name="description" id="description"></textarea>
<select name="status" id="status">...</select>
```

```python
# app.py — inside the POST block
title       = request.form["title"]
description = request.form["description"]
status      = request.form["status"]
```

The `name=` attribute in the HTML and the key in `request.form["..."]` must match exactly — same spelling, same capitalisation. `name="title"` → `request.form["title"]`. Not `"Title"`, not `"task_title"`. Exactly `"title"`.

## The silent failure

This is the most frustrating mistake in this unit:

```html
<!-- name= is missing -->
<input type="text" id="title">
```

```python
title = request.form["title"]  # KeyError — or worse, silently empty
```

Flask doesn't warn you that the name is missing. The field just isn't there in `request.form`. You'll either get a `KeyError` that crashes the POST handler, or — if you use `.get()` instead of `[]` — you'll silently save an empty string. Either way, the cause is a missing `name=` in the HTML.

:::alert{warn}
If your form submits but the data in the CSV is blank or the app crashes on POST, the first thing to check is whether every `<input>`, `<textarea>`, and `<select>` has a `name=` attribute.
:::

## Putting it together

Here's the full round-trip: HTML form field → submitted via POST → read in Flask:

```
name="project_title" in <input>
    ↓ browser sends form data
request.form["project_title"] in Python
    ↓ you assign it
project_title = request.form["project_title"]
    ↓ you save it
writer.writerow({"project_title": project_title, ...})
```

Every field follows this exact pattern. Read all five fields the same way, then pass them all to the CSV writer together.

## `request.form` vs `request.args`

If you built a greet route in Week 1 or Week 2, you may have already used `request.args` — possibly without fully knowing what it was. These two objects look the same but carry data from different places.

::::tabs{id="args-vs-form"}

:::tab{title="request.args — GET form data"}
```html
<!-- Form with no method (defaults to GET) -->
<form action="/greet">
    <input type="text" name="name">
    <button type="submit">Go</button>
</form>
```
```python
# URL becomes: /greet?name=Alice
@app.route("/greet")
def greet():
    name = request.args.get("name")
```
The data travels in the **URL** as query string parameters (`?name=Alice`). You can see it in the address bar. `request.args` reads it.
:::

:::tab{title="request.form — POST form data"}
```html
<!-- Form with method="POST" -->
<form action="/input" method="POST">
    <input type="text" name="title">
    <button type="submit">Save</button>
</form>
```
```python
# URL stays: /input — data is in the request body, not the URL
@app.route("/input", methods=["GET", "POST"])
def input_task():
    if request.method == "POST":
        title = request.form["title"]
```
The data travels in the **request body**, invisible in the URL. `request.form` reads it.
:::

::::

The practical difference for this week:

| | `request.args` | `request.form` |
|---|---|---|
| Form method | GET (or no method) | POST |
| Data visible in URL? | Yes — `?key=value` | No |
| How to read | `request.args.get("key")` | `request.form["key"]` |
| Good for | Short lookups, filters, searches | Saving data, sensitive input |

`request.args` is fine for a greet page where the name is in the URL. It's wrong for saving tasks — you don't want form data appearing in the address bar, and GET requests aren't meant to modify data. Use `request.form` with `method="POST"` for anything that writes.

## Importing `request`

`request` comes from Flask. Make sure your import line includes it:

```python
from flask import Flask, render_template, request, redirect, url_for
```

If `request` isn't imported, Python will throw a `NameError` the first time a form is submitted.

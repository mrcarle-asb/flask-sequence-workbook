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

## Importing `request`

`request` comes from Flask. Make sure your import line includes it:

```python
from flask import Flask, render_template, request, redirect, url_for
```

If `request` isn't imported, Python will throw a `NameError` the first time a form is submitted.

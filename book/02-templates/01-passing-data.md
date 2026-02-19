---
name: Passing Data to Templates
index: 1
---

# Passing Data to Templates

In Week 1, `render_template("page.html")` just read a file and sent it back. But `render_template` can also carry data from Python into the HTML.

## How it works

In `app.py`, you pass keyword arguments:

```python
@app.route("/example")
def example():
    return render_template(
        "example.html",
        page_title="Example Page",
        message="This page uses a template!",
    )
```

In the template, those names become variables you can use:

```html
<h1>{{ page_title }}</h1>
<p>{{ message }}</p>
```

The `{{ }}` delimiters are **Jinja2 syntax**. They mean "output this variable's value here." Flask processes the template, replaces every `{{ }}` with the actual value, and sends the finished HTML to the browser.

:::alert{info}
The browser never sees `{{ }}`. By the time the page arrives, Flask has already filled in every blank. View the page source in your browser to confirm — you'll see plain HTML, not Jinja2.
:::

## The variable name mapping

This is where students get confused. Look carefully:

```python
render_template("tasks.html", tasks=sample_tasks)
```

- **Left side of `=`**: the name inside the template (`tasks`)
- **Right side of `=`**: the Python variable (`sample_tasks`)

Inside `tasks.html`, you use `{{ tasks }}`, **not** `{{ sample_tasks }}`. The template only knows the name you gave it on the left side.

::::tabs{id="naming"}

:::tab{title="Works"}
```python
# app.py
render_template("tasks.html", tasks=sample_tasks)
```
```html
<!-- tasks.html -->
{{ tasks }}
```
:::

:::tab{title="Broken"}
```python
# app.py
render_template("tasks.html", tasks=sample_tasks)
```
```html
<!-- tasks.html — WRONG name -->
{{ sample_tasks }}
```
This renders as nothing. No error — just blank. The template doesn't know what `sample_tasks` is.
:::

::::

:::alert{warn}
Using the same name on both sides (`tasks=tasks`) is clearest and most common. But you must understand the mapping in case you inherit code that uses different names.
:::

## Rename the variable

Now that you understand how the mapping works, rename `sample_tasks` to `tasks` in your Python code. The `sample_` prefix was useful for learning the left-side/right-side distinction, but from here on we'll use matching names — that's the standard convention you'll see in Flask code everywhere.

```python
# Before
sample_tasks = [...]

@app.route("/tasks")
def tasks():
    return render_template("tasks.html", tasks=sample_tasks)

# After
tasks = [...]

@app.route("/tasks")
def show_tasks():
    return render_template("tasks.html", tasks=tasks)
```

Notice `show_tasks` instead of `tasks` for the function name — Python won't let you have a function and a variable with the same name in the same scope. A descriptive verb prefix (`show_`, `list_`, `get_`) is common practice.

## Passing a list of dictionaries

This is the pattern you'll use constantly. Your Python route has a list of data:

```python
tasks = [
    {"title": "Plan IA project", "criterion": "Planning", "date": "2025-09-15"},
    {"title": "Design database schema", "criterion": "Designing", "date": "2025-09-18"},
    {"title": "Build login page", "criterion": "Developing", "date": "2025-09-22"},
]

@app.route("/tasks")
def show_tasks():
    return render_template("tasks.html", tasks=tasks)
```

The template receives the whole list. The next page shows you how to loop over it.

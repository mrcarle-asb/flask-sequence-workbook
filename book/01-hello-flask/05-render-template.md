---
name: Moving HTML Out of Python
index: 5
---

# Moving HTML Out of Python

Look at your route functions. They're returning strings full of HTML tags. For a tiny page that's fine. But imagine a real page — with a `<head>`, CSS links, a navigation bar, a footer, and actual content. That would be a 50-line string jammed inside a Python function.

This is going to get ugly fast. So let's not.

## The `templates/` folder

Flask has a convention: if you create a folder called `templates/` next to your `app.py`, Flask knows to look there for HTML files.

```
your-project/
    app.py
    templates/
        index.html
        about.html
```

These are **plain HTML files**. Nothing special about them yet — just the same HTML you've been writing, but saved in their own files instead of crammed into Python strings.

## `render_template()`

Instead of returning an HTML string, you tell Flask to go find and return an HTML file:

::::tabs{id="render"}

:::tab{title="Before (inline string)"}
```python
@app.route("/")
def index():
    return "<h1>Welcome</h1><p>This is my app.</p>"
```
:::

:::tab{title="After (template file)"}
```python
from flask import Flask, render_template

@app.route("/")
def index():
    return render_template("index.html")
```
:::

::::

That's it. `render_template("index.html")` tells Flask: "go into the `templates/` folder, find `index.html`, read it, and send it back to the browser."

:::alert{info}
You need to import `render_template` from Flask. Update your import line:
```python
from flask import Flask, render_template
```
:::

## What goes in the HTML file

Just normal HTML. Here's what `templates/index.html` might look like:

```html
<!DOCTYPE html>
<html>
<head><title>Home</title></head>
<body>
    <h1>Welcome to the Record of Tasks</h1>
    <p>This is a simple Flask app for tracking IB CS project tasks.</p>
    <p><a href="/">Home</a> | <a href="/about">About</a> | <a href="/tasks">Tasks</a></p>
</body>
</html>
```

No Python. No special syntax. Just an `.html` file.

## Why bother?

::::tabs{id="why"}

:::tab{title="The pain"}
```python
@app.route("/about")
def about():
    return """<!DOCTYPE html>
<html>
<head><title>About</title></head>
<body>
<h1>About This App</h1>
<p>This app helps IB Computer Science students
track their project tasks across the four criteria:
Planning, Designing, Developing, and Testing.</p>
<p><a href="/">Home</a> | <a href="/about">About</a></p>
</body>
</html>"""
```
HTML inside Python. Hard to read. No syntax highlighting for the HTML. Easy to break the quotes. Awful.
:::

:::tab{title="The fix"}
```python
@app.route("/about")
def about():
    return render_template("about.html")
```
One line of Python. All the HTML lives in its own file where you get proper syntax highlighting and can see the structure clearly.
:::

::::

The Python file handles **logic** (which function runs for which URL). The HTML file handles **presentation** (what the page looks like). Separating these two concerns makes both easier to work with.

## The pattern

Every route you write in Week 1 follows this pattern:

```python
@app.route("/somepath")
def somepage():
    return render_template("somepage.html")
```

1. Decorator maps the URL
2. Function runs when that URL is requested
3. `render_template()` finds and returns the HTML file

:::alert{warn}
The filename you pass to `render_template()` must match an actual file inside `templates/`. If Flask can't find it, you'll get a `TemplateNotFound` error. Check your spelling and make sure the file is in the right folder.
:::

## What you've built so far

Take stock. You now have:

- **`app.py`** — Python file with routes that map URLs to functions
- **`templates/`** — folder of HTML files, one per page
- **`render_template()`** — the bridge between them

That's the skeleton of every Flask project. Any CRUD app you build later — including the one you'll design yourself in Week 6 — starts from exactly this structure. Right now the skeleton doesn't do much. Over the next five weeks, you'll add muscles to it: dynamic data, forms, databases, and the tools to manage all three.

## Passing data to a template (preview)

`render_template()` can do more than just read a file — it can pass Python values into the HTML. You'll use this properly in Week 2, but here's the shape of it so it's not a surprise:

```python
# In app.py:
@app.route("/greet/<name>")
def greet(name):
    return render_template("greet.html", name=name)
#                                        ^^^^ keyword argument → available in template
```

```html
<!-- In greet.html: -->
<h1>Hello, {{ name }}!</h1>
<!--              ^^^^ must match the keyword argument name exactly -->
```

The keyword argument name on the Python side (`name=name`) becomes the variable name inside `{{ }}` in the template. They have to match.

:::alert{warn}
**Common mistake:** passing `name=name` in Python but writing `{{ Sarah }}` or `{{ YourName }}` in the template. The curly braces aren't a label — they look up the variable you passed. If the names don't match, you'll get an error or an empty slot.
:::

## What's coming next

Right now these are static HTML files — every visitor sees the exact same page. That's fine for Week 1. But a real application shows data that changes — a list of tasks, a set of records, whatever the app tracks.

`render_template()` is actually capable of more than just reading a file. It can fill in blanks and run simple logic inside HTML — using a system called **Jinja2**. That's Week 2.

For now, just get comfortable with the pattern: Python handles routes, HTML files handle pages. Every week adds one more capability to this skeleton until, by Week 6, you build a complete web application on your own.

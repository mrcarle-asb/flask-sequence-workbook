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

## What's coming in Week 2

Right now these are static HTML files — every visitor sees the exact same page. That's fine for Week 1. But what if you wanted to show different content depending on who visits, or display a list of data from Python?

`render_template()` is actually capable of more than just reading a file. It can fill in blanks and run simple logic inside HTML — using a system called **Jinja2**. That's Week 2. For now, just get comfortable with the pattern: Python handles routes, HTML files handle pages.

---
name: Common Mistakes
index: 6
---

# Common Mistakes

These are the mistakes nearly everyone makes in Week 1. Knowing about them in advance won't prevent all of them, but it might save you 20 minutes of confusion.

## Using `print()` instead of `return`

```python
# WRONG — shows in terminal, browser gets nothing
@app.route("/")
def index():
    print("<h1>Hello</h1>")

# RIGHT — sends HTML to the browser
@app.route("/")
def index():
    return "<h1>Hello</h1>"
```

:::alert{error}
`print()` writes to **your terminal**. `return` sends data to **the browser**. In Flask, you almost always want `return`.
:::

## Forgetting the `@` in the decorator

```python
# WRONG — this is just a function call, not a decorator
app.route("/")
def index():
    return "<h1>Hello</h1>"

# RIGHT
@app.route("/")
def index():
    return "<h1>Hello</h1>"
```

Without the `@`, Python runs `app.route("/")` as a standalone expression (which does nothing useful) and `index()` becomes a regular function that Flask doesn't know about.

## Naming two functions the same thing

```python
@app.route("/")
def page():
    return "<h1>Home</h1>"

@app.route("/about")
def page():          # <-- same name!
    return "<h1>About</h1>"
```

Python silently overwrites the first `page` with the second. The `/` route will break. Give each function a unique name that matches its purpose.

## Returning plain text without HTML tags

```python
@app.route("/")
def index():
    return "Hello World"  # works, but looks plain and unstyled
```

This technically works — the browser shows "Hello World." But without `<h1>`, `<p>`, etc., it's just a wall of unstyled text. At minimum, wrap your content in basic tags.

## `TemplateNotFound` error

```python
@app.route("/about")
def about():
    return render_template("abuot.html")  # typo!
```

The filename you pass to `render_template()` must exactly match a file inside `templates/`. Common causes:
- Typo in the filename
- File is in the wrong folder (sitting next to `app.py` instead of inside `templates/`)
- Wrong capitalization (`About.html` vs `about.html`)

## Forgetting to save before reloading

With `--debug` mode, Flask auto-reloads when you save. But it can't reload what you haven't saved. If your changes aren't appearing, check that you've actually saved the file (`Cmd+S` / `Ctrl+S`).

## The server "isn't working" (it's still running)

If you see no prompt in the terminal, the server is still running. That's correct. It's waiting for requests. Don't close the terminal — open a new one or use the browser.

To stop the server intentionally: `Ctrl+C`.

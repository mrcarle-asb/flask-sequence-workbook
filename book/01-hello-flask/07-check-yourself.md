---
name: Check Yourself
index: 7
---

# Check Yourself

Before you submit, make sure you can answer these questions. If you're stuck on any of them, go back and reread the relevant page.

## Conceptual

:::collapsible{title="What's the difference between flask (lowercase) and Flask (uppercase)?"}
`flask` is the Python library (the LEGO collection). `Flask` is the class inside that library (the specific box that builds the main robot). `from flask import Flask` pulls the class out of the library.
:::

:::collapsible{title="Why does the function use return instead of print?"}
`return` sends data back through Flask to the browser — it becomes the web page. `print()` writes to the terminal only. The browser never sees `print()` output.
:::

:::collapsible{title="What does the @ decorator do?"}
It turns the function below it into a trigger. Instead of running top-to-bottom, the function runs only when Flask receives an HTTP request matching the route path. It's like an event handler.
:::

:::collapsible{title="What happens when you visit a URL that doesn't match any route?"}
Flask returns a 404 Not Found error. It checked every `@app.route` and none matched the requested path.
:::

:::collapsible{title="Why do we move HTML into separate files instead of keeping it in Python strings?"}
Separation of concerns. Python handles the logic (which function runs for which URL). HTML handles the presentation (what the page looks like). Keeping them in separate files means you get proper syntax highlighting for both, and neither file becomes unreadable.
:::

## Practical

::::tabs

:::tab{title="Can you..."}
- [ ] Run `flask run --debug` and see the app in a browser?
- [ ] Add a new route and see it appear without restarting?
- [ ] Use a route variable to capture part of the URL?
- [ ] Add `<a href>` links between your pages?
- [ ] Create an HTML file in `templates/` and serve it with `render_template()`?
- [ ] Stop the server with `Ctrl+C` and start it again?
:::

:::tab{title="Read this code"}
```python
@app.route("/greet/<name>")
def greet(name):
    return f"<h1>Hello, {name}!</h1>"
```

What URL would you visit to see "Hello, Alice!"?

:::collapsible{title="Answer"}
`/greet/Alice` — Flask extracts `Alice` from the URL, passes it to the `name` parameter, and the f-string puts it in the heading.
:::
:::

::::

## What's next

Your app has routes, HTML files in `templates/`, and `render_template()` serving them. But every page is still static — the same HTML every time, for every visitor.

Week 2 introduces **Jinja2** — a way to put placeholders in your HTML files that Flask fills in with Python data. You'll also learn `base.html` and template inheritance, so your nav bar and footer live in one place instead of being copy-pasted across every page.

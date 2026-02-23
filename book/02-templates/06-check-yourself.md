---
name: Check Yourself
index: 7
---

# Check Yourself

## Conceptual

:::collapsible{title="What's the difference between {{ }} and {% %}?"}
`{{ }}` **outputs** a value into the HTML. `{% %}` runs **logic** — loops, conditionals, extends, blocks. Think of `{{ }}` as "print this" and `{% %}` as "do this."
:::

:::collapsible{title="Why does Jinja2 need {% endfor %} when Python doesn't?"}
Python uses indentation to define blocks. HTML doesn't have meaningful indentation — it's just formatting. So Jinja2 needs explicit closing tags to know where a block ends.
:::

:::collapsible{title="What does {% extends \"base.html\" %} do?"}
It tells Jinja2 that this template inherits from `base.html`. Everything in `base.html` (nav, footer, CSS) appears automatically. The child template only fills in the `{% block content %}` placeholder with its own content.
:::

:::collapsible{title="What happens if render_template passes tasks=my_list but the template uses {{ data }}?"}
Nothing renders. The template doesn't know what `data` is — it only knows the name `tasks` (the left side of the `=`). No error, just blank output. This is one of the trickiest bugs because it fails silently.
:::

:::collapsible{title="What does DRY mean and why does base.html follow it?"}
Don't Repeat Yourself. Without `base.html`, every page duplicates the nav, footer, and HTML boilerplate. With it, the shared structure lives in one file. Change it once, it updates everywhere.
:::

## Practical

::::tabs

:::tab{title="Can you..."}
- [ ] Pass a variable from a route to a template and display it with `{{ }}`?
- [ ] Loop over a list of dictionaries with `{% for %}` and display each item?
- [ ] Use `{% if %}` to change how something displays based on a value?
- [ ] Create a `base.html` with a nav bar and footer?
- [ ] Make a child template that extends `base.html` and fills in `{% block content %}`?
- [ ] Add a new page to the nav in `base.html` and see it appear on every page?
:::

:::tab{title="Read this code"}
```python
# app.py
@app.route("/tasks")
def show_tasks():
    return render_template("tasks.html", tasks=tasks)
```
```html
<!-- tasks.html -->
{% extends "base.html" %}
{% block content %}
    {% for task in tasks %}
        {% if task.criterion == "Planning" %}
            <p><strong>{{ task.title }}</strong></p>
        {% else %}
            <p>{{ task.title }}</p>
        {% endif %}
    {% endfor %}
{% endblock %}
```

What does a Planning task look like compared to other tasks?

:::collapsible{title="Answer"}
Planning tasks appear in **bold** (`<strong>` tag). All other tasks appear as normal text. Both are inside `<p>` tags. The `{% if %}` checks the criterion value and wraps Planning tasks differently.
:::
:::

::::

## What's coming next

You can now display data on a page. But where does that data come from? Right now it's hardcoded in `app.py` as a Python list. If you restart the server, nothing changes. If a user wants to add a task, they can't.

Week 3 adds **forms** and **file I/O** — the user submits data through an HTML form, Python receives it, and writes it to a CSV file. That's the **Create** in CRUD, and it's the moment your app goes from read-only to interactive.

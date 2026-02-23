---
name: Common Mistakes
index: 6
---

# Common Mistakes

## Confusing `{{ }}` and `{% %}`

```html
<!-- WRONG — this is a loop, not output -->
{{ for task in tasks }}

<!-- RIGHT — logic uses {% %} -->
{% for task in tasks %}
```

Remember: **`{{ }}` outputs a value.** **`{% %}` runs logic.** If you put a `for` loop inside `{{ }}`, you'll see the literal text "for task in tasks" on the page.

## Forgetting `{% endfor %}` or `{% endif %}`

```html
{% for task in tasks %}
    <li>{{ task.title }}</li>
<!-- MISSING: {% endfor %} -->
```

Jinja2 will throw an error about an "unexpected end of template." Unlike Python, indentation doesn't close blocks. You must explicitly close every `{% for %}` with `{% endfor %}` and every `{% if %}` with `{% endif %}`.

## Using Python syntax in Jinja2

```html
<!-- WRONG — Python dict syntax -->
{{ task["title"] }}

<!-- RIGHT — Jinja2 dot notation -->
{{ task.title }}
```

Both actually work in Jinja2, but dot notation is the convention. The real trap is trying to use Python functions that don't exist in Jinja2 — like `len(tasks)` instead of `tasks|length`.

## Template variable doesn't match the route

```python
# app.py
render_template("tasks.html", task_list=tasks)
```
```html
<!-- tasks.html — WRONG name -->
{% for task in tasks %}
```

The template uses `tasks` but the route passed `task_list`. No error — the loop just silently does nothing because `tasks` is undefined. Check the left side of the `=` in your `render_template()` call.

## Forgetting `{% extends "base.html" %}`

Your page renders but has no nav, no footer, no styling. It's just raw content. The template isn't inheriting from `base.html`.

Fix: make sure `{% extends "base.html" %}` is the **very first line** of the file. Not the second line. Not after a comment. The first line.

## Content outside the block

```html
{% extends "base.html" %}

<h1>This heading disappears</h1>

{% block content %}
    <p>Only this part shows up.</p>
{% endblock %}
```

Anything between `{% extends %}` and `{% block %}` is silently discarded. All your content must go inside the block.

## Putting templates in the wrong folder

```
your-project/
    app.py
    index.html          ← WRONG — not in templates/
    templates/
        base.html
```

`render_template("index.html")` only looks inside `templates/`. If the file is anywhere else, you get `TemplateNotFound`.

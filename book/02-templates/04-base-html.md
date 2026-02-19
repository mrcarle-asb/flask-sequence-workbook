---
name: "base.html and Template Inheritance"
index: 4
---

# base.html and Template Inheritance

Look at the Week 1 templates. Every page had its own `<!DOCTYPE>`, its own `<head>`, its own copy of the nav links. Change the nav? Change it in every file. Add a new page? Copy-paste the boilerplate.

Template inheritance fixes this. You write the shared structure once, and every page fills in only what's different.

## The base template

`base.html` defines the skeleton of every page:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Record of Tasks</title>
    <style>
        body { font-family: Georgia, serif; background-color: #f5f5dc; }
        nav { background-color: #336; padding: 10px 20px; }
        nav a { color: #ddd; text-decoration: none; }
        .content { max-width: 760px; margin: 20px auto; }
        footer { text-align: center; font-size: 12px; color: #666; }
    </style>
</head>
<body>
    <nav>
        <a href="/">Home</a> |
        <a href="/about">About</a> |
        <a href="/tasks">Tasks</a>
    </nav>

    <div class="content">
        {% block content %}{% endblock %}
    </div>

    <footer>M27 IBCS — Flask Sequence</footer>
</body>
</html>
```

The key line is `{% block content %}{% endblock %}`. This is a **placeholder** — a hole in the skeleton that child templates fill in.

## A child template

`tasks.html` extends the base and fills in the block:

```html
{% extends "base.html" %}

{% block content %}
    <h1>All Tasks</h1>
    {% for task in tasks %}
        <li>{{ task.title }} — {{ task.criterion }}</li>
    {% endfor %}
{% endblock %}
```

Three rules:

1. **`{% extends "base.html" %}` must be the first line.** Nothing before it.
2. Everything inside `{% block content %}...{% endblock %}` replaces the placeholder in `base.html`.
3. Everything *outside* the block in `base.html` — the nav, footer, CSS, HTML boilerplate — appears on every page automatically.

## Before and after

::::tabs{id="inheritance"}

:::tab{title="Without base.html"}
```html
<!-- index.html — full page -->
<!DOCTYPE html>
<html>
<head><title>Home</title></head>
<body>
<p><a href="/">Home</a> | <a href="/about">About</a> | <a href="/tasks">Tasks</a></p>
<h1>Welcome to the Record of Tasks</h1>
<p>Track your IB CS project tasks.</p>
</body>
</html>
```
```html
<!-- about.html — another full page, same nav copy-pasted -->
<!DOCTYPE html>
<html>
<head><title>About</title></head>
<body>
<p><a href="/">Home</a> | <a href="/about">About</a> | <a href="/tasks">Tasks</a></p>
<h1>About This App</h1>
<p>Built with Flask for M27 IBCS.</p>
</body>
</html>
```
Every page repeats the same structure. Add a nav link? Edit every file.
:::

:::tab{title="With base.html"}
```html
<!-- index.html -->
{% extends "base.html" %}

{% block content %}
    <h1>Welcome to the Record of Tasks</h1>
    <p>Track your IB CS project tasks.</p>
{% endblock %}
```
```html
<!-- about.html -->
{% extends "base.html" %}

{% block content %}
    <h1>About This App</h1>
    <p>Built with Flask for M27 IBCS.</p>
{% endblock %}
```
Each page is 5-8 lines. The nav, footer, and styling come from one place.
:::

::::

## DRY: Don't Repeat Yourself

This principle has a name: **DRY** — Don't Repeat Yourself. If the same code appears in multiple places, put it in one place and reference it. `base.html` is DRY applied to HTML.

This isn't just about saving typing. It's about **maintaining** the app. When you add a new page to the nav bar next week, you change one line in `base.html` and it appears everywhere. Without inheritance, you'd change it in every file and inevitably miss one.

:::alert{warn}
If your page looks like it has no styling, no nav, or no footer, check two things:
1. Does the template start with `{% extends "base.html" %}`?
2. Is all your content inside `{% block content %}...{% endblock %}`?

Content outside the block is silently ignored.
:::

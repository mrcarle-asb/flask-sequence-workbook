---
name: Completing Your Templates
index: 5
---

# Completing Your Templates

You've read how Jinja2 works. Now you'll apply it. Open your Week 2 starter repo. Your job is to fill in three stub templates so the app works properly. The `/example` route is already done — it's your working reference.

:::alert{warn}
Do not start writing code until you've read this page. The order matters.
:::

## Step 0: Study the working example first

Before touching any stub, open two things side by side:

- `app.py` — find the `/example` route
- `templates/example.html` — open the file

Read them together and answer these three questions in your head:

1. Which line in `example.html` connects it to `base.html`?
2. Where does the page content go — what are the opening and closing tags around it?
3. How does `{{ page_title }}` get its value? Trace it from `app.py` into the template.

Once you can answer all three without looking, you know the pattern. Everything else this week is the same pattern.

---

## Step 1: Complete `index.html` and `about.html`

Open `templates/index.html`. It already extends `base.html` and has an empty `{% block content %}` block. Your job: put something in the block.

```html
{% extends "base.html" %}

{% block content %}
    <h1>Welcome to the Record of Tasks</h1>
    <p>Track your IB CS project tasks across the four criteria.</p>
{% endblock %}
```

Visit `/` in the browser. You should see a proper page with the nav bar and footer from `base.html` and your heading in the middle.

Do the same for `templates/about.html`. A heading and a sentence about what the app does is enough.

:::alert{info}
If your page loads but has no nav bar and no styling, check that `{% extends "base.html" %}` is the first line of the file — not the second line, not after a blank line or comment.
:::

---

## Step 2: Verify the tasks loop

Open `templates/tasks.html`. The `{% for %}` loop is already there — you don't need to write it. Run the app and visit `/tasks`.

You should see a table with five rows: one for each task in `app.py`. If the table is empty or you get an error, check that:

- The route in `app.py` is passing `tasks=tasks` to `render_template`
- The loop in the template says `{% for task in tasks %}` — the name must match the left side of the `=`

---

## Step 3: Add a conditional to `tasks.html`

This is the actual challenge. Find the `{# TODO #}` comment inside the loop. Right now the criterion column just outputs `{{ task.criterion }}` for every row the same way. The assignment asks you to make at least one criterion look visibly different.

Here's the pattern from the [Jinja2 Conditionals](./03-jinja-conditionals) page applied directly to this table cell:

```html
<td>
    {% if task.criterion == "Planning" %}
        <strong>{{ task.criterion }}</strong>
    {% else %}
        {{ task.criterion }}
    {% endif %}
</td>
```

This makes Planning tasks bold. Any visible change counts — you could also add a label, change the background colour with an inline style, or use `{% elif %}` to handle multiple criteria differently.

::::tabs{id="conditional-options"}

:::tab{title="Bold text"}
```html
<td>
    {% if task.criterion == "Planning" %}
        <strong>{{ task.criterion }}</strong>
    {% else %}
        {{ task.criterion }}
    {% endif %}
</td>
```
:::

:::tab{title="Inline colour"}
```html
<td>
    {% if task.criterion == "Planning" %}
        <span style="color: darkblue; font-weight: bold;">{{ task.criterion }}</span>
    {% else %}
        {{ task.criterion }}
    {% endif %}
</td>
```
:::

:::tab{title="Multiple criteria"}
```html
<td>
    {% if task.criterion == "Planning" %}
        <strong>{{ task.criterion }}</strong>
    {% elif task.criterion == "Testing" %}
        <em>{{ task.criterion }}</em>
    {% else %}
        {{ task.criterion }}
    {% endif %}
</td>
```
:::

::::

---

## Step 4 (Stretch): Add your own route

If you've finished the three steps above and want to go further:

1. Add a new route to `app.py` with a function that returns a new template
2. Create the new template file in `templates/` — extend `base.html`, fill in the block
3. Add a link to your new page in the `<nav>` in `base.html`

Once you add it to `base.html`'s nav, the link appears on every page automatically. That's template inheritance paying off.

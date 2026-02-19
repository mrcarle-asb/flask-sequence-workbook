---
name: Jinja2 Loops
index: 2
---

# Jinja2 Loops

You have a list of tasks in Python. You need to display each one on the page. Jinja2's `{% for %}` loop handles this.

## The syntax

```html
{% for task in tasks %}
    <li>{{ task.title }} — {{ task.criterion }}</li>
{% endfor %}
```

Three things to notice:

1. **`{% %}`** is for logic (loops, conditionals). **`{{ }}`** is for output. Different delimiters, different purposes.
2. **`{% endfor %}`** is required. Unlike Python, indentation doesn't define blocks in Jinja2. You must explicitly close every loop and every conditional.
3. **`task.title`** uses dot notation to access dictionary keys. In Python you'd write `task["title"]`. In Jinja2, both work, but dot notation is the convention.

## A complete example

Your route passes the data:

```python
@app.route("/tasks")
def show_tasks():
    return render_template("tasks.html", tasks=tasks)
```

Your template loops over it and builds a table:

```html
<h1>All Tasks</h1>
<table>
    <tr>
        <th>Title</th>
        <th>Criterion</th>
        <th>Date</th>
    </tr>
    {% for task in tasks %}
    <tr>
        <td>{{ task.title }}</td>
        <td>{{ task.criterion }}</td>
        <td>{{ task.date }}</td>
    </tr>
    {% endfor %}
</table>
<p>Total tasks: {{ tasks|length }}</p>
```

:::alert{info}
`{{ tasks|length }}` uses a Jinja2 **filter**. The `|length` filter is like Python's `len()`. Filters are applied with the pipe `|` character.
:::

## What this replaces

Compare this to the Week 1 inline approach:

::::tabs{id="compare"}

:::tab{title="Week 1 (inline string)"}
```python
@app.route("/tasks")
def show_tasks():
    html = "<table>"
    html += "<tr><th>Title</th><th>Criterion</th></tr>"
    for task in tasks:
        html += "<tr>"
        html += "<td>" + task["title"] + "</td>"
        html += "<td>" + task["criterion"] + "</td>"
        html += "</tr>"
    html += "</table>"
    return html
```
Python and HTML mashed together. Unreadable.
:::

:::tab{title="Week 2 (template)"}
```python
@app.route("/tasks")
def show_tasks():
    return render_template("tasks.html", tasks=tasks)
```
One line of Python. All the HTML is in `tasks.html` where it belongs.
:::

::::

## Handling an empty list

What if there's no data yet? In the flask_v2 Record of Tasks app, the template handles this:

```html
{% if tasks|length < 1 %}
    <p>There are no tasks... create one below</p>
{% else %}
    <table>
        {% for task in tasks %}
        <tr>
            <td>{{ task.title }}</td>
            <td>{{ task.date_started.strftime("%Y-%m-%d") }}</td>
        </tr>
        {% endfor %}
    </table>
{% endif %}
```

This pattern — check if the list is empty, show a message if so, otherwise loop — appears in almost every CRUD app.

## `loop.index`

Inside a `{% for %}` block, Jinja2 gives you a special `loop` variable:

```html
{% for step in steps %}
<tr>
    <td>{{ loop.index }}</td>
    <td>{{ step.description }}</td>
</tr>
{% endfor %}
```

`loop.index` counts from 1. `loop.index0` counts from 0. This is useful for numbering rows in a table without needing to track a counter variable yourself.

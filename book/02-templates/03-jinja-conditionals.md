---
name: Jinja2 Conditionals
index: 3
---

# Jinja2 Conditionals

Loops display data. Conditionals change *how* data is displayed depending on its value.

## The syntax

```html
{% if task.criterion == "Planning" %}
    <span class="planning">{{ task.title }}</span>
{% else %}
    <span>{{ task.title }}</span>
{% endif %}
```

Same `{% %}` block syntax as loops. `{% endif %}` is required.

## Why you need conditionals

Consider a Record of Tasks where each task has a completion status:

```html
{% if task.date_completed %}
    <p>Date Completed: {{ task.date_completed.strftime("%Y-%m-%d") }}</p>
{% else %}
    <p>Date Completed: Not yet completed</p>
{% endif %}
```

Without the conditional, trying to call `.strftime()` on a `None` value would crash the template. The `{% if %}` protects you.

## Conditionals inside loops

The real power shows up when you combine loops and conditionals:

```html
{% for task in tasks %}
<tr>
    <td>{{ task.title }}</td>
    <td>
        {% if task.criterion == "Planning" %}
            <strong>{{ task.criterion }}</strong>
        {% elif task.criterion == "Testing" %}
            <em>{{ task.criterion }}</em>
        {% else %}
            {{ task.criterion }}
        {% endif %}
    </td>
    <td>{{ task.date }}</td>
</tr>
{% endfor %}
```

This renders Planning tasks in bold and Testing tasks in italics. Every other criterion displays normally.

:::alert{info}
Jinja2 uses `{% elif %}`, not `{% else if %}`. Same keyword as Python.
:::

## Common patterns

**Show/hide an entire section:**
```html
{% if steps %}
    <h2>Task History</h2>
    <table>
        {% for step in steps %}
        <tr><td>{{ step.description }}</td></tr>
        {% endfor %}
    </table>
{% else %}
    <p>No progress logged for this task yet.</p>
{% endif %}
```

**Conditional CSS class:**
```html
<tr class="{% if task.criterion == 'Planning' %}highlight{% endif %}">
```

**Checking a number:**
```html
{% if task.completion_status == 5 %}
    Complete
{% elif task.completion_status > 0 %}
    In progress ({{ task.completion_status }}/5)
{% else %}
    Not started
{% endif %}
```

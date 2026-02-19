---
name: "Week 2: Templates"
index: 2
---

# Week 2: Templates

Last week you built routes that return HTML. This week, the HTML moves out of Python and into its own files. You'll also learn how to pass data from Python into those files — so the same template can show different content every time.

:::alert{warn}
Same rule as last week: **build it, run it, see it in a browser.** If you haven't typed the code and watched it work, you haven't done the step.
:::

## What you already know

- Flask app setup, `@app.route`, route functions, `return`
- `render_template()` and the `templates/` folder (from the end of Week 1)
- `flask run --debug`

## What's new this week

- Jinja2 variables: `{{ variable }}`
- Jinja2 for loops: `{% for item in list %}...{% endfor %}`
- Jinja2 conditionals: `{% if %}...{% endif %}`
- Template inheritance: `base.html` and `{% extends %}`
- Passing data from routes to templates

## Why this matters

Right now you can serve static pages. But a real application shows *data* — a list of tasks, a set of records, search results. The data lives in Python. The page layout lives in HTML. This week you learn the bridge between them.

By the end of Week 2, you can build any app that displays data on a page. That's half of CRUD — the **Read** part. Weeks 3-5 add the other half.

:::alert{info}
Read the pages in this section in order. Each one builds on the last.
:::

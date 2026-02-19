---
name: Jinja2
---

The templating engine built into Flask. Jinja2 lets you put placeholders (`{{ variable }}`) and logic (`{% for %}`, `{% if %}`) inside HTML files. Flask processes these before sending the page to the browser — the browser never sees the Jinja2 syntax, only the finished HTML.

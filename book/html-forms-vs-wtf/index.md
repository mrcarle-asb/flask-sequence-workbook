---
name: "HTML Forms vs Flask-WTF"
index: 12
---

# Two Ways to Build Forms

Every Flask app that accepts user input has HTML forms. The question is how those forms get built — and where validation happens.

**Manual HTML forms** — you write the `<form>`, `<input>`, and `<select>` tags yourself in the template. The route reads `request.form["field"]` and validates manually.

**Flask-WTF** — you define a form as a Python class in `forms.py`. The route creates a form object and passes it to the template. Jinja renders the fields using `{{ form.field() }}` syntax. Validation rules live on the class.

The punchline: open your browser's "View Source" on a Flask-WTF page. It's the same `<input>` and `<select>` tags you'd write by hand. The abstraction generates HTML — it doesn't replace it.

## What's here

- [**Manual HTML Forms**](01-manual-html-forms) — the approach you already know from Week 3. Write the HTML, read `request.form` in the route.
- [**Flask-WTF Basics**](02-flask-wtf-basics) — define forms as Python classes. Validation, CSRF protection, and Jinja rendering.
- [**Comparison**](03-comparison) — the same form built both ways, and what you get (and give up) with each approach.

## The example

Both pages use the "add a book" form from the [SQLite3 vs ORM](../sqlite-vs-orm) section — title, author (dropdown), and publication year. Simple enough to read quickly, complex enough to show the differences.

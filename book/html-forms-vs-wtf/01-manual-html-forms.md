---
name: "Manual HTML Forms"
index: 1
---

# Manual HTML Forms

This is the approach you've been using since Week 3. You write HTML form elements directly in the template, and the route reads submitted values from `request.form`. No extra libraries.

This page uses the "add a book" form as a running example — a text field for title, a dropdown for author, and a number field for publication year.

---

## The template

```html
<!-- templates/add.html -->

<h1>Add a Book</h1>

<form method="post">
  <div>
    <label for="title">Title</label>
    <input type="text" id="title" name="title" required>
  </div>

  <div>
    <label for="author_id">Author</label>
    <select id="author_id" name="author_id" required>
      <option value="">-- Choose an author --</option>
      {% for author in authors %}
        <option value="{{ author.author_id }}">{{ author.name }}</option>
      {% endfor %}
    </select>
  </div>

  <div>
    <label for="year">Publication Year</label>
    <input type="number" id="year" name="year" min="1000" max="2100">
  </div>

  <button type="submit">Add Book</button>
</form>
```

Everything is explicit. You choose the input types, set the `name` attributes, write the `<label>` tags, and add HTML5 validation attributes like `required` and `min`/`max` yourself.

The `name` attribute on each input is what connects the HTML to `request.form` in the route. `name="title"` in the template means `request.form["title"]` in Python.

---

## The route

```python
@app.route("/add", methods=["GET", "POST"])
def add_book():
    if request.method == "POST":
        title = request.form["title"]
        author_id = request.form["author_id"]
        year = request.form.get("year")  # .get() because year is optional

        # Manual validation
        if not title:
            # handle the error somehow — re-render with a message?
            pass

        # ... save to database ...
        return redirect(url_for("list_books"))

    # GET — load authors for the dropdown
    authors = get_authors()
    return render_template("add.html", authors=authors)
```

Validation is up to you. The `required` attribute in the HTML provides client-side checks (the browser won't submit an empty field), but those can be bypassed. If you need server-side validation, you write the `if not title:` checks yourself.

---

## What you're responsible for

With manual forms, you manage everything:

- **HTML structure** — every `<input>`, `<label>`, `<select>`, and `<option>` tag
- **Field names** — the `name` attribute must match what you read in `request.form`
- **Validation** — both client-side (HTML attributes) and server-side (Python checks)
- **Error display** — if validation fails, re-rendering the form with error messages and preserving what the user already typed
- **CSRF protection** — manual forms have none by default. A malicious site could submit a form to your app on behalf of a logged-in user. You'd need to implement token-based protection yourself.

For a small form this is fine. The code is straightforward, there are no abstractions to learn, and you can see exactly what's happening at every step.

:::alert{info}
This is the approach used in Weeks 3, 4, and 4.5 of the Flask sequence. If you're comfortable with it, you don't need Flask-WTF for simple projects. But read the comparison page to understand what it buys you at larger scale.
:::

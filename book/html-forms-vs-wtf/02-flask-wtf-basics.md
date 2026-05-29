---
name: "Flask-WTF Basics"
index: 2
---

# Flask-WTF Basics

Flask-WTF lets you define a form as a Python class. Instead of writing HTML `<input>` tags in the template, you define fields and validation rules in `forms.py`, pass a form object to Jinja, and let the library render the HTML.

Same "add a book" form — title, author dropdown, publication year.

---

## The form class

```python
# forms.py

from flask_wtf import FlaskForm
from wtforms import StringField, IntegerField, SelectField, SubmitField
from wtforms.validators import DataRequired, Optional, NumberRange


class BookForm(FlaskForm):
    title = StringField("Title", validators=[DataRequired()])
    author_id = SelectField("Author", coerce=int, validators=[DataRequired()])
    year = IntegerField("Publication Year",
                        validators=[Optional(), NumberRange(min=1000, max=2100)])
    submit = SubmitField("Add Book")
```

Each line is a field. The first argument is the label text. `validators` is a list of rules — `DataRequired()` means the field can't be empty, `NumberRange` constrains the value.

**`SelectField` with `coerce=int`** — the dropdown. `coerce=int` means the submitted value is converted to an integer (form data comes in as strings). You populate the choices in the route, not here.

**`SubmitField`** — renders as a submit button. Optional but convenient.

:::collapsible{title="Where do the dropdown choices come from?"}
`SelectField` needs a list of `(value, label)` tuples. You set this in the route after querying the database:

```python
form.author_id.choices = [(a.author_id, a.name) for a in authors]
```

The choices aren't hardcoded in `forms.py` because they come from the database — they're dynamic.
:::

---

## Configuration

Flask-WTF needs a secret key for CSRF protection:

```python
# app.py
app = Flask(__name__)
app.config["SECRET_KEY"] = "your-secret-key-here"
```

In production you'd load this from an environment variable. For class projects, a hardcoded string is fine.

---

## The route

```python
from forms import BookForm

@app.route("/add", methods=["GET", "POST"])
def add_book():
    form = BookForm()
    form.author_id.choices = [(a.author_id, a.name) for a in get_authors()]

    if form.validate_on_submit():
        # form.title.data, form.author_id.data, form.year.data
        # ... save to database ...
        return redirect(url_for("list_books"))

    return render_template("add.html", form=form)
```

Three things to notice:

1. **`form = BookForm()`** — creates the form object. On GET, it's empty. On POST, it automatically populates from `request.form`.
2. **`form.validate_on_submit()`** — returns `True` only if the request is POST *and* all validators pass. This replaces both the `if request.method == "POST"` check and any manual validation.
3. **`form.title.data`** — the cleaned, validated value. This replaces `request.form["title"]`.

If validation fails, the form object remembers what the user typed and carries the error messages. When you re-render the template, the fields are pre-filled and errors are available.

---

## The template

```html
<!-- templates/add.html -->

<h1>Add a Book</h1>

<form method="post">
  {{ form.hidden_tag() }}

  <div>
    {{ form.title.label }}
    {{ form.title(class="form-control") }}
    {% for error in form.title.errors %}
      <span class="error">{{ error }}</span>
    {% endfor %}
  </div>

  <div>
    {{ form.author_id.label }}
    {{ form.author_id(class="form-control") }}
    {% for error in form.author_id.errors %}
      <span class="error">{{ error }}</span>
    {% endfor %}
  </div>

  <div>
    {{ form.year.label }}
    {{ form.year(class="form-control") }}
    {% for error in form.year.errors %}
      <span class="error">{{ error }}</span>
    {% endfor %}
  </div>

  {{ form.submit(class="btn") }}
</form>
```

**`{{ form.hidden_tag() }}`** — renders a hidden CSRF token field. This is the line that gives you cross-site request forgery protection for free.

**`{{ form.title.label }}`** — renders `<label for="title">Title</label>`. The label text comes from `forms.py`.

**`{{ form.title(class="form-control") }}`** — renders the `<input>` tag. You can pass HTML attributes as keyword arguments — `class`, `placeholder`, `id`, anything.

**`form.title.errors`** — a list of validation error messages. Empty if the field is valid. This is how you show "This field is required" next to the right field.

---

## What the browser actually sees

Here's the key insight. Open the page in your browser and View Source. The `{{ form.title() }}` Jinja call renders to:

```html
<input id="title" name="title" required type="text" value="">
```

And `{{ form.hidden_tag() }}` renders to:

```html
<input id="csrf_token" name="csrf_token" type="hidden" value="IjEyMzQ1Njc4OTAi...">
```

And `{{ form.author_id() }}` renders to:

```html
<select id="author_id" name="author_id" required>
  <option value="1">Ursula K. Le Guin</option>
  <option value="2">J.R.R. Tolkien</option>
  <option value="3">Chimamanda Ngozi Adichie</option>
</select>
```

It's all normal HTML. The browser doesn't know or care that it was generated by Flask-WTF. The form class is an abstraction that produces the same `<input>`, `<select>`, and `<label>` tags you'd write by hand — plus a CSRF token you'd otherwise have to implement yourself.

---

## Pre-filling for edit forms

When editing an existing record, you want the form fields to show current values. Pass the object to the form constructor:

```python
@app.route("/edit/<int:book_id>", methods=["GET", "POST"])
def edit_book(book_id):
    book = get_book(book_id)
    form = BookForm(obj=book)
    form.author_id.choices = [(a.author_id, a.name) for a in get_authors()]

    if form.validate_on_submit():
        # save changes ...
        return redirect(url_for("list_books"))

    return render_template("edit.html", form=form)
```

**`BookForm(obj=book)`** — pre-populates every field whose name matches an attribute on `book`. The title field shows the current title, the year field shows the current year, the dropdown selects the current author. With manual forms, you'd set `value="{{ book.title }}"` on each input yourself.

On POST, `form.validate_on_submit()` uses the submitted data, not the pre-filled data. The `obj` argument only matters on GET.

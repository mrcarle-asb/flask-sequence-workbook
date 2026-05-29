---
name: "Comparison"
index: 3
---

# Side by Side

Same form, same fields, same result in the browser. Different code getting there.

---

## Defining the form

### Manual

There is no form definition step. You go straight to writing HTML in the template.

### Flask-WTF

```python
# forms.py

class BookForm(FlaskForm):
    title = StringField("Title", validators=[DataRequired()])
    author_id = SelectField("Author", coerce=int, validators=[DataRequired()])
    year = IntegerField("Publication Year",
                        validators=[Optional(), NumberRange(min=1000, max=2100)])
    submit = SubmitField("Add Book")
```

This is the upfront cost. You're declaring the form's fields and rules before writing any HTML. With manual forms, you skip this step entirely.

**The trade-off:** `forms.py` is more code to write initially. But it's also the single source of truth for what the form accepts and how it validates. With manual forms, that information is split between the HTML template and the route.

---

## The route

### Manual

```python
@app.route("/add", methods=["GET", "POST"])
def add_book():
    if request.method == "POST":
        title = request.form["title"]
        author_id = request.form["author_id"]
        year = request.form.get("year")

        if not title:
            # re-render with error...
            authors = get_authors()
            return render_template("add.html", authors=authors, error="Title is required")

        # ... save to database ...
        return redirect(url_for("list_books"))

    authors = get_authors()
    return render_template("add.html", authors=authors)
```

You check `request.method` yourself. You read `request.form` yourself. You validate yourself. If validation fails, you re-render the template — and you're responsible for passing back what the user typed so the fields aren't blank.

### Flask-WTF

```python
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

One `if` statement replaces the method check and all validation. If it fails, the form object carries both the user's input and the error messages — you just re-render.

---

## The template

### Manual

```html
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

### Flask-WTF

```html
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

The Flask-WTF template is actually *longer* — the `errors` loops add lines. But those error messages are the payoff: when validation fails, each field shows exactly what went wrong, and the form is pre-filled with what the user typed.

To get the same behaviour with manual forms, you'd add the error display logic yourself *and* set `value="{{ request.form.get('title', '') }}"` on each input to preserve user input across failed submissions.

---

## What the browser receives

This is the part that matters. Both versions produce the same HTML in the browser.

### Manual: what you wrote IS what the browser gets

```html
<input type="text" id="title" name="title" required>
```

### Flask-WTF: what the browser gets after Jinja renders

```html
<input class="form-control" id="title" name="title" required type="text" value="">
```

Same tag. Same `name`, same `id`, same `required`. The Flask-WTF version adds the CSS class you passed and an explicit empty `value`. But it's the same `<input>` element. The browser has no idea one was hand-written and the other was generated.

The `{{ form.hidden_tag() }}` line adds one extra element the manual version doesn't have:

```html
<input id="csrf_token" name="csrf_token" type="hidden"
       value="IjEyMzQ1Njc4OTAi.ZnJl...">
```

That's the CSRF token — automatic protection against cross-site form submissions. With manual forms, you don't get this unless you build it yourself.

---

## Validation

### Manual

```python
# In the route — you write every check
if not title:
    error = "Title is required"
if year and (int(year) < 1000 or int(year) > 2100):
    error = "Year must be between 1000 and 2100"
```

You check each field, build error messages, and pass them to the template. If you have 8 fields, you write 8 checks. You also need to handle type conversion — `request.form` gives you strings, so `year` needs to be cast to `int` before comparing.

### Flask-WTF

```python
# In forms.py — declared once
title = StringField("Title", validators=[DataRequired()])
year = IntegerField("Publication Year",
                    validators=[Optional(), NumberRange(min=1000, max=2100)])
```

```python
# In the route — one line
if form.validate_on_submit():
```

Validators run automatically. Type conversion happens automatically (`IntegerField` gives you an `int`). Error messages attach to the field that failed. The template's `form.title.errors` loop displays them.

**The trade-off:** Manual validation is more work but completely transparent. Flask-WTF validation is less work but you need to understand what each validator does — and sometimes the default error messages aren't helpful enough and need customising.

---

## Pre-filling on edit

### Manual

```html
<input type="text" id="title" name="title"
       value="{{ book.title }}" required>

<select id="author_id" name="author_id" required>
  {% for author in authors %}
    <option value="{{ author.author_id }}"
            {% if author.author_id == book.author_id %}selected{% endif %}>
      {{ author.name }}
    </option>
  {% endfor %}
</select>
```

You set `value="{{ book.title }}"` on text inputs and add `selected` conditionally on the right `<option>`. For every field. On every edit form.

### Flask-WTF

```python
form = BookForm(obj=book)
```

One line. The form reads matching attributes from the object and pre-fills every field. The template code is identical to the "add" form — no `value=` attributes, no `selected` conditionals.

**This is where the abstraction pays for itself most visibly.** Edit forms with manual HTML are tedious and error-prone. Edit forms with Flask-WTF are the same template as the add form.

---

## When to use which

### Manual HTML forms are a good fit when:

- The form is simple — a few fields, no complex validation
- You're learning how forms work and want to see every piece
- You don't want to add a dependency (Flask-WTF + WTForms)
- The project has one or two forms total
- You want full control over the HTML structure

### Flask-WTF is a good fit when:

- You have multiple forms across the app
- Forms have validation rules beyond "is this field empty?"
- You need edit forms that pre-fill from existing data
- You want CSRF protection without implementing it yourself
- The project is large enough that `forms.py` is worth maintaining

### The honest version:

For a project with one form that has three fields, Flask-WTF adds a file (`forms.py`), a configuration step (`SECRET_KEY`), an import chain, and a new template syntax — all to save a few lines of validation code. That's not a win.

For a project with five forms, several of which are edit forms, the manual approach means writing `value="{{ thing.field }}"` and `{% if ... %}selected{% endif %}` dozens of times, duplicating validation logic across routes, and having no CSRF protection. That's where the form class starts earning its keep.

The crossover is around the second form with an edit mode. When you're copy-pasting `value=` attributes and `selected` conditionals for the third time, the `BookForm(obj=book)` pattern starts looking less like overhead and more like the point.
